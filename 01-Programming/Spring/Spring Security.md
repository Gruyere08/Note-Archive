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
public class JwtService {

    // secret key used to sign tokens
    private final String SECRET =
            "my-super-secret-key-my-super-secret-key";

    // creates signing key object
    private Key getSigningKey() {
        return Keys.hmacShaKeyFor(SECRET.getBytes());
    }

    // generates token after login
    public String generateToken(UserDetails user) {

        return Jwts.builder()
                .setSubject(user.getUsername()) // identity
                .setIssuedAt(new Date())
                .setExpiration(
                        new Date(System.currentTimeMillis() + 86400000)
                ) // 24h expiration
                .signWith(getSigningKey())
                .compact();
    }

    // extract username from token
    public String extractUsername(String token) {

        return Jwts.parserBuilder()
                .setSigningKey(getSigningKey())
                .build()
                .parseClaimsJws(token)
                .getBody()
                .getSubject();
    }
}
```

## JwtAuthFilter

```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter {

    private final JwtService jwtService;
    private final CustomUserDetailsService userService;

    public JwtAuthFilter(JwtService jwtService,
                         CustomUserDetailsService userService) {
        this.jwtService = jwtService;
        this.userService = userService;
    }

    // SPRING EXECUTES THIS AUTOMATICALLY FOR EACH REQUEST
    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain)
            throws ServletException, IOException {

        // read Authorization header
        String header = request.getHeader("Authorization");

        if (header != null && header.startsWith("Bearer ")) {

            // remove "Bearer "
            String token = header.substring(7);

            // extract username from JWT
            String username = jwtService.extractUsername(token);

            // load user from DB
            UserDetails user =
                    userService.loadUserByUsername(username);

            // create authentication object
            UsernamePasswordAuthenticationToken auth =
                    new UsernamePasswordAuthenticationToken(
                            user,
                            null,
                            user.getAuthorities()
                    );

            // store authentication globally for this request
            SecurityContextHolder
                    .getContext()
                    .setAuthentication(auth);
        }

        // continue request execution
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