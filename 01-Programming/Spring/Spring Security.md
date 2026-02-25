# Step by Step guide to implement Spring security

## Class overview

These are the classes needed to implement a simple Spring security setup

1. User: Doesn't interact with Spring security, it's just part of the model
2. UserRepository: Brings Users from the database
3. SecurityUser: Adapter between your `User` entity and Spring Security. Implements the `UserDetails` interface
4. DatabaseUserDetailsService: Tells Spring how to load users. Connects Spring security with database. Implements the `UserDetailsService` interface
5. JwtService: Handles JWT creation and validation. 
6. JwtAuthFilter: Security checkpoint executed on every request. Authenticates requests using JWT.
7. SecurityConfig: Defines security rules and filter behavior. Central configuration of Spring Security.
8. AuthController: Entry point for authentication. Handles login requests.

## User Entity

```java
@Entity
@Table(name = "users")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true)
    private String username;

    // must contain HASHED password
    @Column(nullable = false)
    private String password;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private Role role;
}
```

Code for the enum:

```java
public enum Role {
    ROLE_USER,
    ROLE_ADMIN
}
```

| Method          | Conceptual Purpose                            |
| --------------- | --------------------------------------------- |
| `getUsername()` | Returns login identity used by authentication |
| `getPassword()` | Returns encrypted password for verification   |
| `getRole()`     | Defines authorization permissions             |


## User Repository

```java
// JpaRepository is PROVIDED by Spring Data JPA
@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    // Spring automatically generates query:
    // SELECT * FROM users WHERE username = ?
    Optional<User> findByUsername(String username);
}
```

| Method             | Conceptual Purpose                         |
| ------------------ | ------------------------------------------ |
| `findByUsername()` | Finds user during login and JWT validation |

## SecurityUser

```java
public class SecurityUser implements UserDetails {

    private final User user;

    public SecurityUser(User user) {
        this.user = user;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return List.of(
                new SimpleGrantedAuthority(user.getRole().name())
        );
    }

    @Override
    public String getPassword() {
        return user.getPassword();
    }

    @Override
    public String getUsername() {
        return user.getUsername();
    }

    // account flags (enable later if needed)
    @Override public boolean isAccountNonExpired() { return true; }
    @Override public boolean isAccountNonLocked() { return true; }
    @Override public boolean isCredentialsNonExpired() { return true; }
    @Override public boolean isEnabled() { return true; }
}
```


## DatabaseUserDetailsService

```java
@Service
@RequiredArgsConstructor
public class DatabaseUserDetailsService implements UserDetailsService {

    private final UserRepository repository;

    @Override
    public UserDetails loadUserByUsername(String username)
            throws UsernameNotFoundException {

        User user = repository.findByUsername(username)
                .orElseThrow(() ->
                        new UsernameNotFoundException("User not found"));

        return new SecurityUser(user);
    }
}
```

| Method                 | Conceptual Purpose                             |
| ---------------------- | ---------------------------------------------- |
| `loadUserByUsername()` | Spring calls this to retrieve user credentials |


## JwtService

```java
@Service
@RequiredArgsConstructor
public class JwtService {

    @Value("${security.jwt.secret}")
    private String secret;

    @Value("${security.jwt.expiration}")
    private long expiration;

    private Key getSigningKey() {
        return Keys.hmacShaKeyFor(secret.getBytes());
    }

    private Claims extractAllClaims(String token) {
        return Jwts.parserBuilder()
                .setSigningKey(getSigningKey())
                .build()
                .parseClaimsJws(token)
                .getBody();
    }

    public String extractUsername(String token) {
        return extractAllClaims(token).getSubject();
    }

    public String generateToken(UserDetails user) {
        return Jwts.builder()
                .setSubject(user.getUsername())
                .setIssuedAt(new Date())
                .setExpiration(
                        new Date(System.currentTimeMillis() + expiration)
                )
                .signWith(getSigningKey())
                .compact();
    }

    public boolean isTokenValid(String token, UserDetails user) {
        String username = extractUsername(token);
        return username.equals(user.getUsername())
                && !isTokenExpired(token);
    }

    private boolean isTokenExpired(String token) {
        return extractAllClaims(token)
                .getExpiration()
                .before(new Date());
    }
}
```

## JwtAuthFilter

```java
@Component
@RequiredArgsConstructor
public class JwtAuthFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final DatabaseUserDetailsService userService;

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain)
            throws ServletException, IOException {

        final String header = request.getHeader("Authorization");

        if (header == null || !header.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }

        String token = header.substring(7);

        try {
            String username = jwtService.extractUsername(token);

            // avoid re-authentication
            if (username != null &&
                SecurityContextHolder.getContext().getAuthentication() == null) {

                UserDetails user =
                        userService.loadUserByUsername(username);

                if (jwtService.isTokenValid(token, user)) {

                    UsernamePasswordAuthenticationToken auth =
                            new UsernamePasswordAuthenticationToken(
                                    user,
                                    null,
                                    user.getAuthorities()
                            );

                    auth.setDetails(
                            new WebAuthenticationDetailsSource()
                                    .buildDetails(request)
                    );

                    SecurityContextHolder.getContext()
                            .setAuthentication(auth);
                }
            }

        } catch (Exception ignored) {
            // invalid JWT → continue without authentication
        }

        filterChain.doFilter(request, response);
    }
}
```

## SecurityConfig

```java
@Configuration
public class SecurityConfig {

    private final JwtAuthFilter jwtFilter;

    public SecurityConfig(JwtAuthFilter jwtFilter) {
        this.jwtFilter = jwtFilter;
    }

    // password hashing bean
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    // MAIN SECURITY CONFIGURATION
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http)
            throws Exception {

        return http
                .csrf(csrf -> csrf.disable()) // disable CSRF for APIs

                // no server sessions → JWT only
                .sessionManagement(session ->
                        session.sessionCreationPolicy(
                                SessionCreationPolicy.STATELESS))

                // endpoint rules
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/auth/**").permitAll()
                        .anyRequest().authenticated()
                )

                // register our JWT filter
                .addFilterBefore(
                        jwtFilter,
                        UsernamePasswordAuthenticationFilter.class
                )

                .build();
    }
}
```

## AuthController

```java
@RestController
@RequestMapping("/auth")
public class AuthController {

    private final AuthenticationManager authenticationManager;
    private final JwtService jwtService;

    public AuthController(AuthenticationManager authenticationManager,
                          JwtService jwtService) {
        this.authenticationManager = authenticationManager;
        this.jwtService = jwtService;
    }

    // LOGIN ENDPOINT
    @PostMapping("/login")
    public String login(@RequestBody LoginRequest request) {

        // Spring validates username + password automatically
        Authentication authentication =
                authenticationManager.authenticate(
                        new UsernamePasswordAuthenticationToken(
                                request.username(),
                                request.password()
                        )
                );

        // retrieve authenticated user
        UserDetails user =
                (UserDetails) authentication.getPrincipal();

        // generate token
        return jwtService.generateToken(user);
    }
}
```