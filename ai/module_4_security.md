# Module 4: Security - Authentication, Authorization & Data Privacy

## 1. Module Overview

### Learning Objectives

By completing this module, you will:

- **Understand** authentication vs authorization and their distinct roles in security
- **Design** secure authentication flows for distributed systems using JWT
- **Implement** role-based access control (RBAC) with granular permissions
- **Apply** OWASP Top 10 security best practices to your API
- **Evaluate** session management strategies in scaled environments
- **Master** password hashing, secure storage, and credential management
- **Implement** comprehensive audit logging for compliance and security monitoring

### Prerequisites

- **Module 1 completed:** REST API with PostgreSQL
- **Module 2 completed:** Multi-database architecture with Redis caching
- **Module 3 completed:** Horizontal scaling with load balancer and multiple instances
- **Familiarity with:**
  - JWT token structure and claims
  - Basic cryptography concepts (hashing, encryption)
  - HTTP security headers
  - OWASP Top 10 vulnerabilities

### Estimated Time Commitment

- **Theory & Design:** 14-18 hours (3-4 days)
- **Implementation:** 28-36 hours (6-8 days of focused work)
- **Break & Observe:** 8-12 hours (2-3 days)
- **Documentation & Reflection:** 4-6 hours
- **Total:** 54-72 hours (approximately 3-4 weeks at moderate pace)

### Key Deliverables

1. **Design Artifacts:**
   - Security architecture diagram with trust boundaries
   - Authentication flow diagrams (login, refresh, logout)
   - Authorization model (roles, permissions, resources)
   - Threat model and security controls mapping
   - Data classification and encryption strategy

2. **Implementation:**
   - JWT-based authentication service
   - User registration with password hashing (bcrypt)
   - Role-based access control (RBAC) system
   - Refresh token rotation mechanism
   - OAuth2 integration (GitHub or Google)
   - Security middleware (CORS, rate limiting, helmet)
   - Audit logging for security events
   - API endpoint protection with guards
   - Password reset flow

3. **Documentation:**
   - Security design decisions
   - API security documentation
   - Incident response procedures
   - Postmortem with security insights

---

## 2. Learning Area Coverage Analysis

### PRIMARY: Security and Compliance

**Concepts Covered:**
- **Authentication:**
  - JWT structure (header, payload, signature)
  - Token generation, validation, and expiration
  - Refresh token rotation and revocation
  - OAuth2 / OpenID Connect integration
  - Multi-factor authentication (MFA) concepts
  - Password hashing with bcrypt (salt + iterations)
- **Authorization:**
  - Role-Based Access Control (RBAC)
  - Permission-based authorization
  - Resource ownership validation
  - Policy-based access control concepts
- **API Security:**
  - OWASP Top 10 protection
  - SQL injection prevention (already covered, reinforced)
  - Cross-Site Scripting (XSS) prevention
  - Cross-Site Request Forgery (CSRF) protection
  - Security headers (Helmet.js)
  - CORS configuration
  - Rate limiting per user (not just per IP)
  - Input validation and sanitization
- **Data Security:**
  - Password storage best practices
  - Encryption at rest (database-level)
  - Encryption in transit (TLS/HTTPS)
  - Sensitive data handling (PII, credentials)
  - Secure token storage (Redis with expiration)
- **Audit and Compliance:**
  - Audit logging of authentication events
  - Security event monitoring
  - Failed login attempt tracking
  - GDPR/privacy considerations

### PRIMARY: System Design

**Concepts Covered:**
- Authentication service architecture
- Session management in distributed systems
- Stateless authentication with JWT
- Token storage strategies (Redis vs database)
- Trust boundaries and security layers
- Service-to-service authentication concepts
- API gateway patterns

### SECONDARY: Observability

**Concepts Covered:**
- Security metrics (failed logins, token issuance)
- Audit log aggregation
- Anomaly detection patterns
- Security event alerting

### SECONDARY: API & Integration Design

**Concepts Covered:**
- OAuth2 flows (authorization code, client credentials)
- OpenID Connect for identity
- API versioning with security changes
- Backward compatibility with authentication

### SECONDARY: Reliability, Scalability, and Availability

**Concepts Covered:**
- Authentication service as potential single point of failure
- Caching authenticated user context
- Token validation performance
- Rate limiting to prevent abuse

### NOT_COVERED: Data and Storage

**Justification:**
Data storage patterns were covered in Modules 1 & 2. This module focuses on securing existing data, not new storage paradigms.

---

## 3. Theory Foundation

### Core Concepts and Principles

#### Authentication vs Authorization

**Authentication:** Who are you?
- Verifying identity (username + password, OAuth, biometrics)
- Issuing proof of identity (JWT token)
- Managing sessions

**Authorization:** What can you do?
- Verifying permissions
- Checking resource ownership
- Enforcing access policies

**Critical Distinction:** Never conflate these. Authentication happens once, authorization happens on every request.

#### JWT (JSON Web Tokens)

**Structure:**
```
Header.Payload.Signature
```

**Header:**
```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**Payload (Claims):**
```json
{
  "sub": "user-id",
  "email": "user@example.com",
  "roles": ["user", "admin"],
  "iat": 1642560000,
  "exp": 1642563600
}
```

**Signature:**
```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

**Pros:**
- Stateless (no server-side session storage)
- Self-contained (includes user info)
- Scalable (works across multiple instances)
- Standard (widely supported)

**Cons:**
- Cannot be revoked easily (until expiration)
- Token size larger than session ID
- Vulnerable if secret leaked
- Requires careful expiration management

#### Refresh Token Pattern

**Problem:** Short-lived access tokens expire frequently, long-lived tokens are risky.

**Solution:**
1. Issue short-lived access token (15 min)
2. Issue long-lived refresh token (7 days)
3. Store refresh token securely (HttpOnly cookie or secure storage)
4. When access token expires, use refresh token to get new one
5. Rotate refresh token on each use (prevent replay attacks)

**Benefits:**
- Limit exposure of access tokens
- Ability to revoke refresh tokens
- Balance security and user experience

#### Password Security

**Never store plaintext passwords. Ever.**

**BCrypt Algorithm:**
```
hash = bcrypt(password, saltRounds)
```

- **Salt:** Random data added to password before hashing
- **Cost Factor (rounds):** Number of iterations (10-12 recommended)
- **Adaptive:** Can increase rounds as hardware improves
- **Slow by design:** Prevents brute force attacks

**Password Validation:**
```typescript
const isValid = await bcrypt.compare(plaintextPassword, hashedPassword);
```

#### OAuth2 Flows

**Authorization Code Flow (for web apps):**
```
1. User clicks "Login with Google"
2. Redirect to Google auth page
3. User grants permission
4. Google redirects back with authorization code
5. Exchange code for access token
6. Use access token to get user info
7. Create session in your app
```

**Benefits:**
- Don't handle passwords yourself
- Leverage trusted identity providers
- SSO (Single Sign-On) experience

#### Role-Based Access Control (RBAC)

**Roles:**
- User
- Admin
- Moderator
- (Custom roles as needed)

**Permissions:**
- `notes:read`
- `notes:write`
- `notes:delete`
- `users:manage`

**Authorization Logic:**
```typescript
if (user.roles.includes('admin') || 
    (user.roles.includes('user') && resource.ownerId === user.id)) {
  // Allow access
}
```

**Policy-Based Extensions:**
- Time-based access
- IP-based restrictions
- MFA requirements for sensitive operations

#### OWASP Top 10 (2021)

1. **Broken Access Control** → Implement RBAC, verify ownership
2. **Cryptographic Failures** → Use bcrypt, HTTPS, encrypt sensitive data
3. **Injection** → Input validation, parameterized queries (already covered)
4. **Insecure Design** → Threat modeling, security by design
5. **Security Misconfiguration** → Security headers, disable debug in production
6. **Vulnerable Components** → Keep dependencies updated
7. **Authentication Failures** → Multi-factor auth, secure password policy
8. **Integrity Failures** → Sign JWTs, verify integrity
9. **Logging Failures** → Comprehensive audit logging
10. **SSRF** → Validate URLs, whitelist allowed domains

### Recommended Reading Materials

#### Essential Reading (Before Implementation)

1. **OWASP Top 10** (3-4 hours)
   - https://owasp.org/www-project-top-ten/
   - Focus: Understanding common vulnerabilities

2. **JWT.io Introduction** (1-2 hours)
   - https://jwt.io/introduction
   - Focus: Token structure, claims, validation

3. **OWASP Authentication Cheat Sheet** (2-3 hours)
   - https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html
   - Focus: Best practices for auth implementation

4. **OWASP Authorization Cheat Sheet** (2 hours)
   - https://cheatsheetseries.owasp.org/cheatsheets/Authorization_Cheat_Sheet.html
   - Focus: RBAC patterns

5. **"Web Security for Developers" (selected chapters)** (4-5 hours)
   - Chapters on authentication, authorization, and session management

#### Supplementary Reading (During Implementation)

6. **NestJS Authentication Documentation** (2 hours)
   - https://docs.nestjs.com/security/authentication
   - https://docs.nestjs.com/security/authorization

7. **Passport.js Strategies** (1-2 hours)
   - JWT strategy
   - OAuth strategies (Google, GitHub)

8. **GDPR Compliance for Developers** (2 hours)
   - https://gdpr.eu/
   - Focus: Data privacy requirements

### Key Terminology and Definitions

- **Authentication (AuthN):** Verifying identity
- **Authorization (AuthZ):** Verifying permissions
- **JWT:** JSON Web Token, self-contained auth token
- **Claims:** Statements about user in JWT payload
- **Bearer Token:** Token presented in Authorization header
- **Refresh Token:** Long-lived token to obtain new access tokens
- **Salt:** Random data added to password before hashing
- **bcrypt:** Adaptive password hashing algorithm
- **OAuth2:** Authorization framework for delegated access
- **OpenID Connect (OIDC):** Identity layer on top of OAuth2
- **RBAC:** Role-Based Access Control
- **Principal:** Authenticated user or service
- **Resource Owner:** User who owns the data
- **CORS:** Cross-Origin Resource Sharing
- **CSRF:** Cross-Site Request Forgery
- **XSS:** Cross-Site Scripting
- **MFA:** Multi-Factor Authentication

### Common Patterns and Anti-Patterns

#### Patterns to Follow

✅ **Short-Lived Access Tokens:** 15-30 minutes expiration
✅ **Refresh Token Rotation:** New refresh token on each use
✅ **Password Hashing with bcrypt:** 10-12 salt rounds
✅ **HttpOnly Cookies for Tokens:** Prevent XSS token theft
✅ **HTTPS Everywhere:** Encrypt all traffic
✅ **Rate Limiting:** Prevent brute force attacks
✅ **Audit Logging:** Log all authentication events
✅ **Principle of Least Privilege:** Grant minimal necessary permissions
✅ **Input Validation:** Validate and sanitize all inputs
✅ **Security Headers:** Use Helmet.js for HTTP headers

#### Anti-Patterns to Avoid

❌ **Storing Passwords in Plaintext:** NEVER
❌ **Long-Lived Access Tokens:** Increases attack surface
❌ **Tokens in URL:** Can leak in logs, history
❌ **Rolling Your Own Crypto:** Use established libraries
❌ **Trusting Client Data:** Always verify server-side
❌ **Exposing User IDs Sequentially:** Use UUIDs
❌ **No Rate Limiting on Auth Endpoints:** Brute force vulnerability
❌ **Returning Detailed Error Messages:** "Invalid credentials" not "Password wrong"
❌ **No Token Revocation Strategy:** Cannot invalidate compromised tokens
❌ **Hardcoded Secrets:** Use environment variables

### Best Practices Specific to This Module

**Authentication:**
1. Implement account lockout after failed login attempts (e.g., 5 failures = 15 min lockout)
2. Use bcrypt with at least 10 rounds (12 recommended)
3. Require strong passwords (min length, complexity)
4. Issue short-lived JWT access tokens (15-30 min)
5. Implement refresh token rotation
6. Log all authentication events (success and failure)

**Authorization:**
1. Check permissions on every protected route
2. Verify resource ownership (user can only access own notes)
3. Use guards consistently across controllers
4. Implement role hierarchy (admin includes user permissions)
5. Fail closed (deny by default)

**Token Management:**
1. Store JWT secret in environment variable (never commit)
2. Use strong secret (256-bit minimum)
3. Validate token signature on every request
4. Check token expiration
5. Store refresh tokens in Redis with expiration

**API Security:**
1. Enable CORS with specific origins (not `*`)
2. Use Helmet.js for security headers
3. Implement rate limiting per user (not just IP)
4. Validate all inputs with class-validator
5. Return generic error messages (don't leak info)

**Data Privacy:**
1. Never log passwords (even hashed)
2. Redact sensitive data in logs
3. Implement data access audit trail
4. Support data deletion (GDPR right to erasure)
5. Encrypt sensitive data at rest

---

## 4. Practical Implementation Plan

### Phase 1: Design (14-18 hours)

#### Architecture Decisions to Make

**1. Authentication Strategy:**
- JWT vs session-based? (JWT for stateless, scalable)
- Token storage: Redis, database, or memory? (Redis for distributed)
- Token expiration: 15 min access, 7 days refresh
- Refresh token rotation strategy

**2. Authorization Model:**
- Roles: User, Admin, Moderator?
- Permissions: Resource-based (notes:read, notes:write) or coarse-grained?
- How to store: User table with roles column? Separate roles table?

**3. OAuth Integration:**
- Which providers? (GitHub, Google)
- User linking strategy (email matching)
- Account creation flow

**4. Security Controls:**
- Rate limiting thresholds (login: 5 attempts/15 min)
- Password policy (min 8 chars, 1 uppercase, 1 number)
- Session timeout
- Account lockout policy

#### Design Artifacts to Create

**1. Security Architecture Diagram:**

```
┌──────────────┐
│   Client     │
└──────┬───────┘
       │ HTTPS
       ▼
┌─────────────────────┐
│   Nginx (LB)        │
│   - SSL Termination │
│   - Rate Limiting   │
└──────┬──────────────┘
       │
┌──────▼──────────────────────────┐
│  API Instances (Stateless)      │
│  ┌────────────────────────┐     │
│  │  Auth Guard            │     │
│  │  - Validate JWT        │     │
│  │  - Check permissions   │     │
│  └────────┬───────────────┘     │
│           │                     │
│  ┌────────▼───────────┐         │
│  │  Controllers       │         │
│  │  (Protected)       │         │
│  └────────────────────┘         │
└─────────────────────────────────┘
       │          │
       │          ▼
       │    ┌──────────┐
       │    │  Redis   │
       │    │ (Tokens) │
       │    └──────────┘
       │
       ▼
┌──────────────┐
│  PostgreSQL  │
│  (Users,     │
│   Roles)     │
└──────────────┘
```

**2. Authentication Flow Diagram:**

**Registration:**
```
1. User submits email + password
2. Validate email format, password strength
3. Check email not already registered
4. Hash password with bcrypt (12 rounds)
5. Create user record
6. Send verification email (optional)
7. Return success (don't auto-login)
```

**Login:**
```
1. User submits email + password
2. Rate limit check (max 5 attempts)
3. Find user by email
4. Compare password with bcrypt
5. If invalid: Log failed attempt, return generic error
6. If valid: Generate JWT access token (15 min)
7. Generate refresh token (7 days)
8. Store refresh token in Redis
9. Return tokens
10. Log successful login
```

**Token Refresh:**
```
1. Client sends refresh token
2. Validate refresh token signature
3. Check token in Redis (not revoked)
4. Generate new access token
5. Generate new refresh token (rotation)
6. Delete old refresh token from Redis
7. Store new refresh token in Redis
8. Return new tokens
```

**Logout:**
```
1. Client sends refresh token
2. Delete refresh token from Redis
3. Return success
4. Client discards access token
```

**3. Authorization Model:**

**Users Table:**
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  roles VARCHAR[] DEFAULT ARRAY['user'],
  is_active BOOLEAN DEFAULT true,
  failed_login_attempts INT DEFAULT 0,
  locked_until TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**Roles & Permissions:**
```typescript
enum Role {
  User = 'user',
  Admin = 'admin',
  Moderator = 'moderator',
}

const RolePermissions = {
  user: [
    'notes:read:own',
    'notes:write:own',
    'notes:delete:own',
    'profile:read:own',
    'profile:update:own',
  ],
  moderator: [
    ...RolePermissions.user,
    'notes:read:any',
    'notes:delete:any',
  ],
  admin: [
    ...RolePermissions.moderator,
    'users:read:any',
    'users:update:any',
    'users:delete:any',
  ],
};
```

**4. Threat Model:**

| Threat | Likelihood | Impact | Mitigation |
|--------|-----------|--------|------------|
| Credential Stuffing | High | High | Rate limiting, account lockout, MFA |
| JWT Secret Leak | Low | Critical | Strong secret, rotation strategy, environment var |
| Token Theft (XSS) | Medium | High | HttpOnly cookies, CSP headers, input sanitization |
| Token Theft (MITM) | Medium | High | HTTPS only, HSTS header |
| Brute Force | High | Medium | Rate limiting, strong passwords, account lockout |
| SQL Injection | Low | Critical | Parameterized queries (already implemented) |
| CSRF | Medium | Medium | CSRF tokens, SameSite cookies |
| Unauthorized Access | Medium | High | RBAC, resource ownership checks |
| Session Fixation | Low | Medium | Generate new token on login |

**5. Data Classification:**

| Data Type | Classification | Storage | Encryption | Retention |
|-----------|---------------|---------|------------|-----------|
| Passwords | Critical | Never store plaintext | bcrypt hash | Forever (or until user deletes account) |
| Email | PII | PostgreSQL | At-rest encryption | Per user request (GDPR) |
| JWT Secret | Critical | Environment variable | N/A | Rotate quarterly |
| Access Token | Sensitive | Client-side (memory) | Signed (not encrypted) | 15 minutes |
| Refresh Token | Sensitive | Redis | At-rest encryption | 7 days |
| Audit Logs | Compliance | MongoDB | At-rest encryption | 1 year minimum |

#### Technology Choices and Justifications

| Technology | Choice | Justification |
|------------|--------|---------------|
| Auth Framework | Passport.js | Industry standard, pluggable strategies |
| JWT Library | @nestjs/jwt | NestJS native, based on jsonwebtoken |
| Password Hashing | bcrypt | Adaptive, industry standard, secure |
| OAuth Provider | GitHub + Google | Popular, well-documented |
| Security Headers | Helmet.js | Comprehensive, maintained |
| Token Storage | Redis | Fast, distributed, TTL support |

### Phase 2: Core Implementation (28-36 hours)

#### Step 1: User Entity and Repository (3-4 hours)

**1. Create Users Module:**

```bash
nest generate module users
nest generate service users
nest generate controller users
```

**2. Create User Entity:**

```typescript
// users/entities/user.entity.ts
@Entity('users')
export class User {
  @PrimaryGeneratedColumn('uuid')
  id: string;

  @Column({ unique: true })
  @Index()
  email: string;

  @Column()
  @Exclude() // Don't expose in responses
  passwordHash: string;

  @Column('simple-array', { default: 'user' })
  roles: string[];

  @Column({ default: true })
  isActive: boolean;

  @Column({ default: 0 })
  failedLoginAttempts: number;

  @Column({ type: 'timestamp', nullable: true })
  lockedUntil: Date | null;

  @Column({ type: 'timestamp', nullable: true })
  lastLoginAt: Date | null;

  @CreateDateColumn()
  createdAt: Date;

  @UpdateDateColumn()
  updatedAt: Date;
}
```

**3. Create Migration:**

```bash
npm run migration:generate -- -n CreateUsersTable
npm run migration:run
```

#### Step 2: Authentication Service (6-8 hours)

**1. Install Dependencies:**

```bash
npm install @nestjs/jwt @nestjs/passport passport passport-jwt passport-local
npm install bcrypt
npm install --save-dev @types/bcrypt @types/passport-jwt @types/passport-local
```

**2. Create Auth Module:**

```typescript
// auth/auth.module.ts
@Module({
  imports: [
    UsersModule,
    PassportModule,
    JwtModule.register({
      secret: process.env.JWT_SECRET,
      signOptions: { expiresIn: '15m' },
    }),
    CacheModule.register(), // Redis for refresh tokens
  ],
  providers: [AuthService, JwtStrategy, LocalStrategy],
  controllers: [AuthController],
  exports: [AuthService],
})
export class AuthModule {}
```

**3. Implement Auth Service:**

```typescript
// auth/auth.service.ts
@Injectable()
export class AuthService {
  constructor(
    private usersService: UsersService,
    private jwtService: JwtService,
    @Inject(CACHE_MANAGER) private cacheManager: Cache,
  ) {}

  async register(registerDto: RegisterDto): Promise<User> {
    const { email, password } = registerDto;

    // Check if user exists
    const existing = await this.usersService.findByEmail(email);
    if (existing) {
      throw new ConflictException('Email already registered');
    }

    // Validate password strength
    this.validatePasswordStrength(password);

    // Hash password
    const passwordHash = await bcrypt.hash(password, 12);

    // Create user
    return this.usersService.create({
      email,
      passwordHash,
      roles: ['user'],
    });
  }

  async login(user: User): Promise<AuthResponse> {
    // Check if account is locked
    if (user.lockedUntil && new Date() < user.lockedUntil) {
      throw new UnauthorizedException('Account locked. Try again later.');
    }

    // Generate tokens
    const tokens = await this.generateTokens(user);

    // Update last login
    await this.usersService.updateLastLogin(user.id);

    // Reset failed attempts
    await this.usersService.resetFailedAttempts(user.id);

    // Log successful login
    await this.auditLog('LOGIN_SUCCESS', user.id);

    return tokens;
  }

  async validateUser(email: string, password: string): Promise<User | null> {
    const user = await this.usersService.findByEmail(email);

    if (!user) {
      await this.auditLog('LOGIN_FAILED', null, { email, reason: 'User not found' });
      return null;
    }

    if (!user.isActive) {
      await this.auditLog('LOGIN_FAILED', user.id, { reason: 'Account inactive' });
      throw new UnauthorizedException('Account is inactive');
    }

    const isPasswordValid = await bcrypt.compare(password, user.passwordHash);

    if (!isPasswordValid) {
      await this.handleFailedLogin(user);
      await this.auditLog('LOGIN_FAILED', user.id, { reason: 'Invalid password' });
      return null;
    }

    return user;
  }

  async refreshTokens(refreshToken: string): Promise<AuthResponse> {
    try {
      // Verify refresh token
      const payload = this.jwtService.verify(refreshToken, {
        secret: process.env.JWT_REFRESH_SECRET,
      });

      // Check if refresh token is in Redis (not revoked)
      const storedToken = await this.cacheManager.get(`refresh:${payload.sub}`);
      if (!storedToken || storedToken !== refreshToken) {
        throw new UnauthorizedException('Invalid refresh token');
      }

      // Get user
      const user = await this.usersService.findById(payload.sub);
      if (!user || !user.isActive) {
        throw new UnauthorizedException('User not found or inactive');
      }

      // Generate new tokens
      const tokens = await this.generateTokens(user);

      // Delete old refresh token
      await this.cacheManager.del(`refresh:${user.id}`);

      // Log token refresh
      await this.auditLog('TOKEN_REFRESH', user.id);

      return tokens;
    } catch (error) {
      throw new UnauthorizedException('Invalid refresh token');
    }
  }

  async logout(userId: string): Promise<void> {
    // Delete refresh token from Redis
    await this.cacheManager.del(`refresh:${userId}`);

    // Log logout
    await this.auditLog('LOGOUT', userId);
  }

  private async generateTokens(user: User): Promise<AuthResponse> {
    const payload = {
      sub: user.id,
      email: user.email,
      roles: user.roles,
    };

    // Access token (15 minutes)
    const accessToken = this.jwtService.sign(payload);

    // Refresh token (7 days)
    const refreshToken = this.jwtService.sign(payload, {
      secret: process.env.JWT_REFRESH_SECRET,
      expiresIn: '7d',
    });

    // Store refresh token in Redis
    await this.cacheManager.set(
      `refresh:${user.id}`,
      refreshToken,
      7 * 24 * 60 * 60 // 7 days in seconds
    );

    return {
      accessToken,
      refreshToken,
      user: this.sanitizeUser(user),
    };
  }

  private async handleFailedLogin(user: User): Promise<void> {
    const attempts = user.failedLoginAttempts + 1;

    if (attempts >= 5) {
      // Lock account for 15 minutes
      const lockedUntil = new Date(Date.now() + 15 * 60 * 1000);
      await this.usersService.lockAccount(user.id, lockedUntil);
    } else {
      await this.usersService.incrementFailedAttempts(user.id);
    }
  }

  private validatePasswordStrength(password: string): void {
    if (password.length < 8) {
      throw new BadRequestException('Password must be at least 8 characters');
    }
    if (!/[A-Z]/.test(password)) {
      throw new BadRequestException('Password must contain uppercase letter');
    }
    if (!/[a-z]/.test(password)) {
      throw new BadRequestException('Password must contain lowercase letter');
    }
    if (!/[0-9]/.test(password)) {
      throw new BadRequestException('Password must contain number');
    }
  }

  private sanitizeUser(user: User): Partial<User> {
    const { passwordHash, failedLoginAttempts, lockedUntil, ...sanitized } = user;
    return sanitized;
  }

  private async auditLog(event: string, userId: string | null, metadata?: any) {
    // Log to MongoDB activity collection
    await this.activityService.log({
      event,
      userId,
      timestamp: new Date(),
      metadata,
    });
  }
}
```

**4. Create Auth Controller:**

```typescript
// auth/auth.controller.ts
@Controller('auth')
export class AuthController {
  constructor(private authService: AuthService) {}

  @Post('register')
  @HttpCode(HttpStatus.CREATED)
  async register(@Body() registerDto: RegisterDto) {
    return this.authService.register(registerDto);
  }

  @UseGuards(LocalAuthGuard)
  @Post('login')
  @HttpCode(HttpStatus.OK)
  async login(@Request() req) {
    return this.authService.login(req.user);
  }

  @Post('refresh')
  @HttpCode(HttpStatus.OK)
  async refresh(@Body() refreshDto: RefreshTokenDto) {
    return this.authService.refreshTokens(refreshDto.refreshToken);
  }

  @UseGuards(JwtAuthGuard)
  @Post('logout')
  @HttpCode(HttpStatus.NO_CONTENT)
  async logout(@Request() req) {
    await this.authService.logout(req.user.id);
  }

  @UseGuards(JwtAuthGuard)
  @Get('me')
  async getProfile(@Request() req) {
    return req.user;
  }
}
```

#### Step 3: JWT Strategy and Guards (4-5 hours)

**1. JWT Strategy:**

```typescript
// auth/strategies/jwt.strategy.ts
@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
  constructor(private usersService: UsersService) {
    super({
      jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
      ignoreExpiration: false,
      secretOrKey: process.env.JWT_SECRET,
    });
  }

  async validate(payload: any) {
    // This is called after JWT signature validation
    // Payload is the decoded JWT

    const user = await this.usersService.findById(payload.sub);

    if (!user || !user.isActive) {
      throw new UnauthorizedException('User not found or inactive');
    }

    // This object is attached to request.user
    return {
      id: user.id,
      email: user.email,
      roles: user.roles,
    };
  }
}
```

**2. Local Strategy (for login):**

```typescript
// auth/strategies/local.strategy.ts
@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
  constructor(private authService: AuthService) {
    super({
      usernameField: 'email',
      passwordField: 'password',
    });
  }

  async validate(email: string, password: string): Promise<any> {
    const user = await this.authService.validateUser(email, password);

    if (!user) {
      throw new UnauthorizedException('Invalid credentials');
    }

    return user;
  }
}
```

**3. JWT Auth Guard:**

```typescript
// auth/guards/jwt-auth.guard.ts
@Injectable()
export class JwtAuthGuard extends AuthGuard('jwt') {
  canActivate(context: ExecutionContext) {
    // Add custom logic here if needed
    return super.canActivate(context);
  }

  handleRequest(err, user, info) {
    if (err || !user) {
      throw err || new UnauthorizedException('Invalid token');
    }
    return user;
  }
}
```

#### Step 4: Role-Based Access Control (5-6 hours)

**1. Roles Decorator:**

```typescript
// auth/decorators/roles.decorator.ts
export const ROLES_KEY = 'roles';
export const Roles = (...roles: string[]) => SetMetadata(ROLES_KEY, roles);
```

**2. Roles Guard:**

```typescript
// auth/guards/roles.guard.ts
@Injectable()
export class RolesGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const requiredRoles = this.reflector.getAllAndOverride<string[]>(ROLES_KEY, [
      context.getHandler(),
      context.getClass(),
    ]);

    if (!requiredRoles) {
      return true; // No roles required, allow access
    }

    const request = context.switchToHttp().getRequest();
    const user = request.user;

    if (!user) {
      return false;
    }

    return requiredRoles.some((role) => user.roles?.includes(role));
  }
}
```

**3. Resource Ownership Guard:**

```typescript
// auth/guards/resource-ownership.guard.ts
@Injectable()
export class ResourceOwnershipGuard implements CanActivate {
  async canActivate(context: ExecutionContext): Promise<boolean> {
    const request = context.switchToHttp().getRequest();
    const user = request.user;
    const resourceId = request.params.id;

    if (!user) {
      return false;
    }

    // Admin can access any resource
    if (user.roles.includes('admin')) {
      return true;
    }

    // Check if user owns the resource
    // This requires injecting the resource service
    // For now, assume resource has ownerId field
    const resource = await this.getResource(resourceId);

    if (!resource) {
      throw new NotFoundException('Resource not found');
    }

    return resource.ownerId === user.id;
  }

  private async getResource(id: string): Promise<any> {
    // Inject appropriate service based on resource type
    // This is a simplified example
  }
}
```

**4. Apply Guards to Controllers:**

```typescript
// notes/notes.controller.ts
@Controller('api/v1/notes')
@UseGuards(JwtAuthGuard) // All routes require authentication
export class NotesController {
  constructor(private notesService: NotesService) {}

  @Get()
  async findAll(@Request() req, @Query() query: PaginationDto) {
    // Filter notes by user (unless admin)
    const userId = req.user.roles.includes('admin') ? undefined : req.user.id;
    return this.notesService.findAll(query, userId);
  }

  @Get(':id')
  @UseGuards(ResourceOwnershipGuard) // User can only access own notes
  async findOne(@Param('id') id: string) {
    return this.notesService.findOne(id);
  }

  @Post()
  async create(@Request() req, @Body() createNoteDto: CreateNoteDto) {
    // Automatically set owner to current user
    return this.notesService.create({
      ...createNoteDto,
      ownerId: req.user.id,
    });
  }

  @Delete(':id')
  @Roles('admin', 'moderator') // Only admin or moderator can delete
  async remove(@Param('id') id: string) {
    return this.notesService.remove(id);
  }
}
```

#### Step 5: OAuth2 Integration (4-5 hours)

**1. Install OAuth Packages:**

```bash
npm install passport-google-oauth20 passport-github2
npm install --save-dev @types/passport-google-oauth20 @types/passport-github2
```

**2. GitHub Strategy:**

```typescript
// auth/strategies/github.strategy.ts
@Injectable()
export class GithubStrategy extends PassportStrategy(Strategy, 'github') {
  constructor(private authService: AuthService) {
    super({
      clientID: process.env.GITHUB_CLIENT_ID,
      clientSecret: process.env.GITHUB_CLIENT_SECRET,
      callbackURL: process.env.GITHUB_CALLBACK_URL,
      scope: ['user:email'],
    });
  }

  async validate(accessToken: string, refreshToken: string, profile: any) {
    const { id, username, emails, photos } = profile;

    // Find or create user
    let user = await this.authService.findByOAuthId('github', id);

    if (!user) {
      // Check if user exists by email
      const email = emails[0].value;
      user = await this.authService.findByEmail(email);

      if (user) {
        // Link OAuth account to existing user
        await this.authService.linkOAuthAccount(user.id, 'github', id);
      } else {
        // Create new user
        user = await this.authService.createFromOAuth({
          provider: 'github',
          providerId: id,
          email,
          username,
          avatar: photos[0].value,
        });
      }
    }

    return user;
  }
}
```

**3. OAuth Controller:**

```typescript
// auth/auth.controller.ts (additional routes)
@Get('github')
@UseGuards(AuthGuard('github'))
async githubAuth() {
  // Redirects to GitHub
}

@Get('github/callback')
@UseGuards(AuthGuard('github'))
async githubAuthCallback(@Request() req) {
  // User is available in req.user
  return this.authService.login(req.user);
}
```

#### Step 6: Security Middleware and Headers (3-4 hours)

**1. Install Helmet:**

```bash
npm install helmet
```

**2. Configure Helmet:**

```typescript
// main.ts
import helmet from 'helmet';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // Security headers
  app.use(helmet({
    contentSecurityPolicy: {
      directives: {
        defaultSrc: ["'self'"],
        styleSrc: ["'self'", "'unsafe-inline'"],
        scriptSrc: ["'self'"],
        imgSrc: ["'self'", 'data:', 'https:'],
      },
    },
    hsts: {
      maxAge: 31536000,
      includeSubDomains: true,
      preload: true,
    },
  }));

  // CORS
  app.enableCors({
    origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
    credentials: true,
    methods: ['GET', 'POST', 'PUT', 'PATCH', 'DELETE'],
    allowedHeaders: ['Content-Type', 'Authorization'],
  });

  await app.listen(3000);
}

bootstrap();
```

**3. CSRF Protection (if using cookies):**

```bash
npm install csurf
```

```typescript
// main.ts
import * as csurf from 'csurf';

app.use(csurf({ cookie: true }));
```

**4. Rate Limiting per User:**

```typescript
// auth/guards/user-rate-limit.guard.ts
@Injectable()
export class UserRateLimitGuard implements CanActivate {
  constructor(@Inject(CACHE_MANAGER) private cacheManager: Cache) {}

  async canActivate(context: ExecutionContext): Promise<boolean> {
    const request = context.switchToHttp().getRequest();
    const user = request.user;

    if (!user) {
      return true; // Let auth guard handle unauthenticated requests
    }

    const key = `rate-limit:${user.id}`;
    const limit = 100; // 100 requests
    const window = 60; // per 60 seconds

    const current = await this.cacheManager.get<number>(key) || 0;

    if (current >= limit) {
      throw new HttpException('Rate limit exceeded', HttpStatus.TOO_MANY_REQUESTS);
    }

    await this.cacheManager.set(key, current + 1, window);
    return true;
  }
}
```

#### Step 7: Audit Logging (3-4 hours)

**1. Create Audit Interceptor:**

```typescript
// common/interceptors/audit.interceptor.ts
@Injectable()
export class AuditInterceptor implements NestInterceptor {
  constructor(private auditService: AuditService) {}

  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const request = context.switchToHttp().getRequest();
    const user = request.user;
    const startTime = Date.now();

    return next.handle().pipe(
      tap(async (response) => {
        const duration = Date.now() - startTime;

        await this.auditService.log({
          userId: user?.id,
          action: `${request.method} ${request.url}`,
          resource: this.getResource(request),
          resourceId: request.params?.id,
          ip: request.ip,
          userAgent: request.headers['user-agent'],
          duration,
          timestamp: new Date(),
          success: true,
        });
      }),
      catchError(async (error) => {
        const duration = Date.now() - startTime;

        await this.auditService.log({
          userId: user?.id,
          action: `${request.method} ${request.url}`,
          resource: this.getResource(request),
          resourceId: request.params?.id,
          ip: request.ip,
          userAgent: request.headers['user-agent'],
          duration,
          timestamp: new Date(),
          success: false,
          error: error.message,
        });

        throw error;
      }),
    );
  }

  private getResource(request: any): string {
    // Extract resource from URL
    const parts = request.url.split('/');
    return parts[3] || 'unknown'; // e.g., /api/v1/notes -> notes
  }
}
```

**2. Apply Audit Interceptor:**

```typescript
// main.ts
app.useGlobalInterceptors(new AuditInterceptor(app.get(AuditService)));
```

### Phase 3: Enhancement (6-8 hours)

#### 1. Multi-Factor Authentication (MFA) (3 hours)

**Implement TOTP (Time-based One-Time Password):**

```bash
npm install speakeasy qrcode
npm install --save-dev @types/speakeasy @types/qrcode
```

```typescript
// auth/mfa.service.ts
@Injectable()
export class MfaService {
  async generateSecret(user: User): Promise<{ secret: string; qrCode: string }> {
    const secret = speakeasy.generateSecret({
      name: `Back2Code (${user.email})`,
      length: 32,
    });

    const qrCode = await QRCode.toDataURL(secret.otpauth_url);

    return {
      secret: secret.base32,
      qrCode,
    };
  }

  async enableMfa(userId: string, secret: string, token: string): Promise<void> {
    const isValid = speakeasy.totp.verify({
      secret,
      encoding: 'base32',
      token,
      window: 2,
    });

    if (!isValid) {
      throw new BadRequestException('Invalid MFA token');
    }

    await this.usersService.enableMfa(userId, secret);
  }

  async verifyMfa(user: User, token: string): Promise<boolean> {
    if (!user.mfaSecret) {
      return true; // MFA not enabled
    }

    return speakeasy.totp.verify({
      secret: user.mfaSecret,
      encoding: 'base32',
      token,
      window: 2,
    });
  }
}
```

#### 2. Password Reset Flow (2 hours)

**Implement password reset via email:**

```typescript
// auth/auth.service.ts (additional methods)
async requestPasswordReset(email: string): Promise<void> {
  const user = await this.usersService.findByEmail(email);

  if (!user) {
    // Don't reveal if email exists
    return;
  }

  // Generate reset token
  const resetToken = randomBytes(32).toString('hex');
  const resetTokenHash = await bcrypt.hash(resetToken, 10);

  // Store token with expiration (1 hour)
  await this.cacheManager.set(
    `password-reset:${resetTokenHash}`,
    user.id,
    60 * 60 // 1 hour
  );

  // Send email (integrate with email service)
  await this.emailService.sendPasswordReset(user.email, resetToken);

  // Log password reset request
  await this.auditLog('PASSWORD_RESET_REQUESTED', user.id);
}

async resetPassword(token: string, newPassword: string): Promise<void> {
  const tokenHash = await bcrypt.hash(token, 10);
  const userId = await this.cacheManager.get<string>(`password-reset:${tokenHash}`);

  if (!userId) {
    throw new BadRequestException('Invalid or expired reset token');
  }

  // Validate new password
  this.validatePasswordStrength(newPassword);

  // Hash new password
  const passwordHash = await bcrypt.hash(newPassword, 12);

  // Update password
  await this.usersService.updatePassword(userId, passwordHash);

  // Delete reset token
  await this.cacheManager.del(`password-reset:${tokenHash}`);

  // Revoke all refresh tokens (force re-login)
  await this.cacheManager.del(`refresh:${userId}`);

  // Log password reset
  await this.auditLog('PASSWORD_RESET_COMPLETED', userId);
}
```

#### 3. Security Event Monitoring (2 hours)

**Create security monitoring service:**

```typescript
// security/security-monitor.service.ts
@Injectable()
export class SecurityMonitorService {
  constructor(
    @Inject(CACHE_MANAGER) private cacheManager: Cache,
    private alertService: AlertService,
  ) {}

  @Cron('*/5 * * * *') // Every 5 minutes
  async monitorSecurityEvents() {
    await this.checkFailedLoginAttempts();
    await this.checkSuspiciousActivity();
  }

  private async checkFailedLoginAttempts() {
    const failedLogins = await this.auditService.getFailedLoginsSince(
      new Date(Date.now() - 5 * 60 * 1000) // Last 5 minutes
    );

    if (failedLogins.length > 50) {
      await this.alertService.send({
        severity: 'high',
        message: `High number of failed login attempts: ${failedLogins.length}`,
        details: failedLogins,
      });
    }
  }

  private async checkSuspiciousActivity() {
    // Check for multiple IPs for same user
    // Check for unusual access patterns
    // Check for privilege escalation attempts
    // etc.
  }
}
```

#### 4. API Key Authentication (1 hour)

**For service-to-service authentication:**

```typescript
// auth/strategies/api-key.strategy.ts
@Injectable()
export class ApiKeyStrategy extends PassportStrategy(Strategy, 'api-key') {
  constructor(private authService: AuthService) {
    super({
      header: 'X-API-Key',
      prefix: '',
    });
  }

  async validate(apiKey: string) {
    const user = await this.authService.validateApiKey(apiKey);

    if (!user) {
      throw new UnauthorizedException('Invalid API key');
    }

    return user;
  }
}
```

---

## 5. Complexity Validation

### Too Basic Indicators (Not Present)

- ❌ Simple authentication without tokens
- ❌ No authorization (everyone can access everything)
- ❌ Storing passwords in plaintext
- ❌ No security headers or CORS
- ❌ No audit logging

### Appropriate Indicators (Present) ✅

- ✅ **JWT authentication** in distributed system
- ✅ **RBAC** with roles and permissions
- ✅ **bcrypt** password hashing with salt
- ✅ **Refresh token rotation** for security
- ✅ **OAuth2 integration** with GitHub/Google
- ✅ **Security headers** (Helmet.js)
- ✅ **Rate limiting** per user
- ✅ **Audit logging** for compliance
- ✅ **OWASP Top 10** awareness and mitigations
- ✅ **Account lockout** after failed attempts

### Too Advanced Indicators (Not Present)

- ❌ Advanced MFA (biometrics, hardware keys)
- ❌ Distributed session management (Redis Cluster)
- ❌ Advanced threat detection (ML-based)
- ❌ Zero-trust architecture
- ❌ Advanced OAuth flows (PKCE, device flow)
- ❌ Fine-grained permissions (attribute-based access control)

### Complexity Rating: **ADVANCED**

### Justification

This module is **advanced** because:

**1. Security in Distributed Systems:**
- JWT validation across multiple instances
- Stateless authentication challenges
- Token revocation in distributed cache
- Session management complexity

**2. Production Security Patterns:**
- Refresh token rotation (prevents replay attacks)
- Account lockout (prevents brute force)
- Audit logging (compliance requirement)
- OAuth integration (real-world SSO)

**3. Cryptography Understanding:**
- bcrypt salt and rounds
- JWT signature validation
- Token expiration management
- Secure random generation

**4. Authorization Complexity:**
- RBAC with multiple roles
- Resource ownership validation
- Permission-based guards
- Cross-cutting security concerns

**5. Threat Awareness:**
- OWASP Top 10 vulnerabilities
- Attack prevention strategies
- Security monitoring and alerts
- Incident response

**Why Appropriate for Your Profile:**
- Security is critical at senior+ levels
- Distributed auth complexity mirrors real systems
- Trade-offs between security and UX
- Compliance requirements in enterprise

⚠️ **CONCERN:** Security is complex and mistakes have serious consequences.

**Mitigation:**
- Use established libraries (bcrypt, Passport.js)
- Follow OWASP guidelines
- Comprehensive testing including security tests
- Audit logging to detect issues

---

## 6. Best Practices Integration

### Security Headers

```typescript
// Implemented via Helmet.js
- X-Content-Type-Options: nosniff
- X-Frame-Options: DENY
- X-XSS-Protection: 1; mode=block
- Strict-Transport-Security: max-age=31536000
- Content-Security-Policy: (configured)
```

### Password Security

```typescript
// Best practices
- Minimum 8 characters
- Require uppercase, lowercase, number
- Hash with bcrypt (12 rounds)
- Never log passwords (even hashed)
- Secure password reset flow
- Force re-login after password change
```

### Token Management

```typescript
// Best practices
- Short-lived access tokens (15 min)
- Long-lived refresh tokens (7 days)
- Refresh token rotation
- Store refresh tokens securely (Redis)
- Revoke tokens on logout
- Validate signature on every request
```

### Authorization

```typescript
// Best practices
- Check permissions on every request
- Verify resource ownership
- Fail closed (deny by default)
- Use guards consistently
- Log authorization failures
```

### Audit Logging

```typescript
// What to log
- Authentication events (login, logout, failed attempts)
- Authorization failures
- Data access (read, write, delete)
- Security events (password reset, MFA)
- Administrative actions

// What NOT to log
- Passwords (even hashed)
- Full tokens
- Sensitive PII unnecessarily
```

---

## 7. "Break & Observe" Exercises

### Exercise 1: Brute Force Attack Simulation

**What to Break:**
```bash
# Attempt login with wrong password repeatedly
for i in {1..20}; do
  curl -X POST http://localhost/auth/login \
    -H "Content-Type: application/json" \
    -d '{"email":"user@example.com","password":"wrongpassword"}'
  echo "Attempt $i"
  sleep 0.5
done
```

**Expected Behavior:**
- First 5 attempts: Return "Invalid credentials"
- After 5 attempts: Account locked for 15 minutes
- Subsequent attempts: "Account locked. Try again later."
- Failed attempts logged in audit trail

**What to Observe:**
- Database: `failed_login_attempts` increments
- Database: `locked_until` timestamp set after 5 failures
- Audit logs: Multiple `LOGIN_FAILED` events
- Security monitoring: Alert if implemented

**Key Learnings:**
- Importance of account lockout
- Rate limiting at application level
- Audit trail for security analysis
- Balance security vs user experience (15 min lockout)

---

### Exercise 2: JWT Token Theft and Reuse

**What to Break:**
```bash
# 1. Login and capture token
TOKEN=$(curl -X POST http://localhost/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password"}' \
  | jq -r '.accessToken')

# 2. Use token to access protected endpoint
curl http://localhost/api/v1/notes \
  -H "Authorization: Bearer $TOKEN"

# 3. Logout
curl -X POST http://localhost/auth/logout \
  -H "Authorization: Bearer $TOKEN"

# 4. Try to use same token again
curl http://localhost/api/v1/notes \
  -H "Authorization: Bearer $TOKEN"
```

**Expected Behavior:**
- After logout: Refresh token deleted from Redis
- Access token: Still valid until expiration (15 min)
- Cannot get new access token (refresh token revoked)

**What to Observe:**
- Access token works even after logout (until expiration)
- Refresh token revoked (cannot refresh)
- Short token lifetime limits exposure

**Key Learnings:**
- **Critical insight:** Access tokens cannot be revoked
- Short expiration time limits damage
- Refresh token revocation prevents long-term access
- Trade-off: security vs performance (checking Redis every request)

**Enhancement Exercise:**
Implement token blacklist:
```typescript
async validateToken(token: string): Promise<boolean> {
  const isBlacklisted = await this.cacheManager.get(`blacklist:${token}`);
  return !isBlacklisted;
}
```

---

### Exercise 3: Role Escalation Attempt

**What to Break:**
```bash
# 1. Login as regular user
USER_TOKEN=$(curl -X POST http://localhost/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password"}' \
  | jq -r '.accessToken')

# 2. Try to access admin-only endpoint
curl -X DELETE http://localhost/api/v1/users/some-id \
  -H "Authorization: Bearer $USER_TOKEN"

# 3. Manually modify JWT (decode, change roles, re-encode without signature)
# This will fail signature validation
```

**Expected Behavior:**
- Admin endpoint returns 403 Forbidden
- Manually modified token fails signature validation
- Audit log records authorization failure

**Key Learnings:**
- JWT signature prevents tampering
- Roles checked on every request
- Authorization separate from authentication
- Fail closed: deny if no permission

---

### Exercise 4: OAuth Account Linking

**What to Break:**
```bash
# 1. Register user with email user@example.com
curl -X POST http://localhost/auth/register \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password"}'

# 2. Login via GitHub with same email
# Open browser: http://localhost/auth/github

# 3. Check if accounts linked correctly
curl http://localhost/auth/me \
  -H "Authorization: Bearer $TOKEN"
```

**Expected Behavior:**
- OAuth login finds existing user by email
- Links GitHub account to existing user
- User can login via password OR GitHub

**What to Observe:**
- Database: `oauth_provider` and `oauth_id` populated
- User has both password and OAuth login methods
- Audit log: `OAUTH_LINKED` event

**Key Learnings:**
- Account linking strategy
- Email as primary identifier
- Supporting multiple auth methods
- Security: verify email before linking

---

### Exercise 5: CORS and XSS Testing

**What to Break:**
```bash
# 1. Try to make request from different origin (without CORS)
# Create HTML file:
cat > test-cors.html << 'EOF'
<!DOCTYPE html>
<html>
<body>
<script>
fetch('http://localhost:3000/api/v1/notes', {
  headers: { 'Authorization': 'Bearer YOUR_TOKEN' }
})
.then(r => r.json())
.then(console.log)
.catch(console.error);
</script>
</body>
</html>
EOF

# 2. Open file://test-cors.html in browser
# Should fail with CORS error

# 3. Try XSS in note content
curl -X POST http://localhost/api/v1/notes \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"title":"Test","content":"<script>alert(\"XSS\")</script>"}'

# 4. Retrieve note and check if script is sanitized
```

**Expected Behavior:**
- CORS blocks requests from unauthorized origins
- XSS content sanitized or escaped
- CSP headers prevent inline script execution

**Key Learnings:**
- CORS protects against cross-origin attacks
- Input sanitization prevents XSS
- Security headers provide defense in depth
- Client-side AND server-side validation needed

---

### Exercise 6: Password Reset Flow

**What to Break:**
```bash
# 1. Request password reset
curl -X POST http://localhost/auth/password-reset/request \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com"}'

# 2. Capture reset token from email/logs (in dev)
RESET_TOKEN="..."

# 3. Try to use token twice
curl -X POST http://localhost/auth/password-reset/confirm \
  -H "Content-Type: application/json" \
  -d '{"token":"'$RESET_TOKEN'","newPassword":"newpassword123"}'

# Should succeed first time

curl -X POST http://localhost/auth/password-reset/confirm \
  -H "Content-Type: application/json" \
  -d '{"token":"'$RESET_TOKEN'","newPassword":"anotherpassword"}'

# Should fail second time (token already used)

# 4. Try to use old password
curl -X POST http://localhost/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"oldpassword"}'

# Should fail
```

**Expected Behavior:**
- Reset token can only be used once
- Token expires after 1 hour
- Old password no longer works
- All refresh tokens revoked (force re-login on all devices)

**Key Learnings:**
- Single-use tokens prevent replay attacks
- Time-limited tokens reduce risk
- Password change should invalidate all sessions
- Balance security vs user convenience

---

## 8. Success Criteria

### Knowledge: Can Explain Concepts and Trade-offs

**You should be able to explain:**

1. **Authentication vs Authorization:**
   - ✅ Clear distinction and examples
   - ✅ When each is checked in request lifecycle

2. **JWT:**
   - ✅ Structure (header, payload, signature)
   - ✅ Stateless vs stateful session management
   - ✅ When to use JWT vs sessions
   - ✅ JWT vulnerabilities and mitigations

3. **Password Security:**
   - ✅ Why plaintext storage is catastrophic
   - ✅ How bcrypt works (salt, rounds)
   - ✅ Why MD5/SHA1 are insufficient
   - ✅ Password strength requirements

4. **OAuth2:**
   - ✅ Authorization code flow
   - ✅ Benefits of delegated authentication
   - ✅ Account linking strategies

5. **RBAC:**
   - ✅ Roles vs permissions
   - ✅ Resource ownership validation
   - ✅ When RBAC is sufficient vs ABAC

### Skills: Can Implement Without Assistance

**You should be able to:**

1. **Authentication:**
   - ✅ Implement JWT authentication from scratch
   - ✅ Create registration and login flows
   - ✅ Implement refresh token rotation
   - ✅ Hash passwords with bcrypt
   - ✅ Integrate OAuth2 provider

2. **Authorization:**
   - ✅ Create role-based guards
   - ✅ Implement resource ownership checks
   - ✅ Apply guards to controllers
   - ✅ Design permission models

3. **Security:**
   - ✅ Configure security headers (Helmet)
   - ✅ Setup CORS correctly
   - ✅ Implement rate limiting
   - ✅ Create audit logging
   - ✅ Secure sensitive data

4. **Testing:**
   - ✅ Test authentication flows
   - ✅ Test authorization rules
   - ✅ Security test cases (invalid tokens, role escalation)

### Application: Can Diagnose and Fix Issues

**You should be able to:**

1. **Debug Auth Issues:**
   - ✅ Diagnose "Unauthorized" errors
   - ✅ Validate JWT signature issues
   - ✅ Debug CORS problems
   - ✅ Investigate failed login attempts

2. **Security Analysis:**
   - ✅ Review audit logs for suspicious activity
   - ✅ Identify security vulnerabilities
   - ✅ Respond to security incidents
   - ✅ Implement security patches

3. **Performance:**
   - ✅ Optimize token validation
   - ✅ Cache user permissions
   - ✅ Reduce auth overhead

---

## 9. Postmortem Template

**Module:** Module 4: Security - Authentication, Authorization & Data Privacy  
**Completed Date:** ___________  
**Actual Time Spent:** _____ hours (vs estimated 54-72 hours)

### What Worked Well?

**Technical Implementation:**
- Was JWT authentication straightforward?
- How effective was bcrypt for password security?
- Did OAuth integration work smoothly?
- Were guards and decorators intuitive?

**Learning Process:**
- Which security concepts were clearest?
- Did "Break & Observe" exercises deepen understanding?
- Were OWASP guidelines helpful?
- Did threat modeling clarify risks?

**Tools and Technologies:**
- How was Passport.js for authentication?
- Was Helmet.js easy to configure?
- Did Redis work well for token storage?
- Were security headers effective?

### What Was Challenging?

**Conceptual Challenges:**
- Understanding JWT vs session trade-offs?
- Grasping OAuth2 flows?
- Designing RBAC model?
- Threat modeling complexity?

**Technical Difficulties:**
- JWT signature validation issues?
- OAuth callback configuration?
- CORS problems?
- Refresh token rotation bugs?
- Audit logging implementation?

**Security Complexity:**
- Balancing security vs usability?
- Understanding cryptography?
- Testing security features?
- Handling edge cases (account lockout, password reset)?

### Key Technical Insights Gained

**Authentication:**
- What did you learn about JWT in practice?
- How does distributed authentication differ from single-instance?
- What surprised you about password security?

**Authorization:**
- How does RBAC work in real applications?
- What are the challenges of resource ownership?
- When is RBAC sufficient vs more complex models?

**Security:**
- Which OWASP vulnerabilities are most relevant?
- How effective are security headers?
- What is the cost of security (performance, complexity)?

**Trade-offs:**
- Security vs user experience?
- Stateless vs stateful authentication?
- Short vs long token expiration?

### Design Trade-offs Made and Rationale

**Decision 1: _____________**
(e.g., "15 minute access token expiration")

**Decision 2: _____________**
(e.g., "RBAC vs ABAC")

**Decision 3: _____________**
(e.g., "Refresh token rotation vs long-lived tokens")

### What Would You Do Differently Next Time?

**Architecture:**
- Different token expiration strategy?
- More/fewer roles?
- Different OAuth providers?

**Implementation:**
- Start with different auth library?
- More comprehensive testing?
- Different audit logging approach?

**Security:**
- Stricter password policy?
- Additional security layers (MFA)?
- More paranoid defaults?

### How Does This Connect to Real-World Production Systems?

**Patterns You've Seen:**
- JWT auth in production systems?
- OAuth SSO implementations?
- RBAC in enterprise applications?
- Audit logging for compliance?

**Gaps vs Production Reality:**
- Advanced MFA (biometrics, hardware keys)?
- Fine-grained permissions (ABAC)?
- Advanced threat detection?
- SOC 2 / ISO 27001 compliance?

**Operational Considerations:**
- How to respond to security incidents?
- Key rotation strategy?
- Monitoring for security events?
- Compliance audits?

### Module-Specific Questions

**Authentication:**
- How confident are you implementing auth from scratch?
- What auth patterns remain unclear?
- When would you use sessions vs JWT?

**Authorization:**
- Can you design RBAC for complex systems?
- How would you handle permission changes?
- When is RBAC insufficient?

**Security:**
- How would you approach security audit?
- What security controls would you add?
- How to stay updated on vulnerabilities?

### Quantitative Results

**Security Metrics:**
- Failed login attempts handled: ___
- Account lockout triggered after: ___ attempts
- Token expiration time: ___ minutes
- Password hash time: ___ms (bcrypt rounds: ___)

**Performance Impact:**
- Auth overhead per request: ___ms
- Token validation time: ___ms
- Password hashing time: ___ms

### Questions to Explore Further

1. ___________________________________
2. ___________________________________
3. ___________________________________
4. ___________________________________
5. ___________________________________

### Preparation for Next Module

**Foundation Built:**
- Secured distributed system
- User context available for logs
- Auth required for accessing resources

**Skills to Strengthen:**
- Advanced auth patterns?
- Security testing?
- Compliance knowledge?

**Mindset:**
- Confidence level going into Module 5: ___/10
- Excitement level for observability: ___/10
- Energy level: ___/10

### Overall Reflection

**Most Valuable Learning:**
_What single security insight will most impact your work?_

**Biggest Surprise:**
_What was most unexpected about security implementation?_

**Pride Moment:**
_What security feature are you most proud of?_

**Advice to Your Past Self:**
_What would you tell yourself before starting Module 4?_

---

## 10. Coverage Gap Analysis

### ✅ Excellently Covered Areas

**Security and Compliance (PRIMARY):**
- JWT authentication in distributed system
- bcrypt password hashing
- OAuth2 integration
- RBAC implementation
- Audit logging
- OWASP Top 10 awareness
- **Verdict:** Comprehensive coverage, core module strength

**System Design (PRIMARY):**
- Auth service architecture
- Stateless authentication
- Token management in distributed system
- **Verdict:** Strong coverage of security architecture

### ⚠️ Adequately Covered Areas

**Observability (SECONDARY):**
- Security metrics
- Audit logs
- **Gap:** No visualization (deferred to Module 5)
- **Verdict:** Adequate foundation

**API & Integration Design (SECONDARY):**
- OAuth2 flows
- **Gap:** No API gateway patterns (deferred to Module 6)
- **Verdict:** Sufficient for security focus

### Critical Concepts Missing

**1. Advanced MFA:**
- **Status:** Basic TOTP in Phase 3
- **Recommendation:** Sufficient for learning, production would need more

**2. Advanced Authorization (ABAC):**
- **Status:** Not covered
- **Recommendation:** RBAC sufficient for most cases, mention ABAC as next step

**3. Security Testing:**
- **Gap:** No dedicated security testing framework
- **Recommendation:** Add to Phase 3

### Adjustments to Ensure Comprehensive Coverage

**Add to Phase 3:**
1. **Security Testing:**
   - OWASP ZAP for automated scanning
   - Manual penetration testing exercises
   - SQL injection tests (verify protection)

2. **API Key Authentication:**
   - Service-to-service auth
   - API key generation and rotation

**Add to Theory:**
1. **Zero Trust Architecture:**
   - Concepts and principles
   - How it differs from perimeter security

2. **Compliance Frameworks:**
   - GDPR, SOC 2, ISO 27001 overview
   - What they require from engineering

### Final Coverage Assessment

| Learning Area | Coverage Level | Depth | Breadth | Module Fit | Change from Module 3 |
|---------------|----------------|-------|---------|------------|---------------------|
| Security      | PRIMARY        | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Perfect | ↑↑↑ Massive addition |
| System Design | PRIMARY        | ⭐⭐⭐⭐ | ⭐⭐⭐⭐ | Excellent | ↑ Security architecture |
| Observability | SECONDARY      | ⭐⭐⭐ | ⭐⭐⭐ | Good | ↑ Security metrics |
| API & Integration | SECONDARY   | ⭐⭐⭐ | ⭐⭐⭐ | Good | ↑ OAuth integration |
| Reliability   | SECONDARY      | ⭐⭐ | ⭐⭐ | Adequate | = Similar to Module 3 |
| Data & Storage | NOT_COVERED    | - | - | Appropriate | = No change |

---

## 11. Next Module Recommendations

### Module 5: Observability - Monitoring, Logging & Alerting

**Primary Focus:**
- Prometheus metrics collection
- Grafana dashboards
- Centralized logging (ELK or Loki)
- Distributed tracing (Jaeger)
- Alerting rules and incident response
- SLIs, SLOs, and SLAs

**Why This Sequence:**

1. **Natural Progression:**
   - Module 3: Scaled system (need to monitor)
   - Module 4: Secured system (need to monitor security)
   - Module 5: Observable system ← NEXT

2. **Operational Necessity:**
   - Can't operate what you can't see
   - Security events need monitoring
   - Performance issues need tracking
   - Alerts prevent incidents

3. **Leverages Existing Infrastructure:**
   - Multiple instances generate rich metrics
   - Security events to visualize
   - Cache hit rates to track
   - Database performance to monitor

**Key Learning Objectives:**
- Set up Prometheus and Grafana
- Create meaningful dashboards
- Implement distributed tracing
- Design alerting rules
- Practice incident response

**Dependencies Satisfied by Module 4:**
✅ User context for access logs
✅ Security events to monitor
✅ Audit logs to aggregate
✅ Auth metrics (login success/failure rates)

---

## Validation Checklist

- [x] **All PRIMARY learning objectives are clear and measurable**
- [x] **Theory foundation covers WHY, not just HOW**
- [x] **Implementation is broken into digestible phases**
- [x] **Complexity is appropriate for the learner profile**
- [x] **At least 5 best practices are explicitly integrated**
- [x] **Break & Observe exercises are practical and insightful**
- [x] **Success criteria are specific and testable**
- [x] **Postmortem prompts encourage deep reflection**
- [x] **Module fits within the progressive learning path**
- [x] **No critical learning gaps exist**

---

## ✅ VALIDATED: This module provides appropriate depth and breadth for the stated learning objectives.

### Strengths of This Module Plan:

**1. Comprehensive Security Coverage:**
- Authentication (JWT, OAuth)
- Authorization (RBAC)
- Password security (bcrypt)
- OWASP Top 10 awareness
- Audit logging

**2. Production-Ready Patterns:**
- Refresh token rotation
- Account lockout
- Security headers
- Rate limiting per user

**3. Distributed System Context:**
- Stateless authentication across instances
- Token storage in Redis
- Security in scaled environment

**4. Practical Security Testing:**
- 6 Break & Observe exercises
- Real attack simulation
- Understanding vulnerabilities through practice

**5. Industry Standards:**
- JWT (widely adopted)
- OAuth2 (SSO standard)
- bcrypt (password hashing standard)
- OWASP guidelines (security baseline)

### Why This Works for Your Profile:

**Engineering Leadership:**
- Security is board-level concern
- Understanding trade-offs (security vs UX)
- Compliance requirements (audit logs)
- Risk assessment (threat modeling)

**Senior Engineer Skills:**
- Production security patterns
- Cryptography understanding
- Security architecture
- Incident response

**Career Relevance:**
- Security breaches are costly
- Authentication is fundamental
- Every system needs auth
- Security expertise is valuable

### Success Probability: 90%

**Enablers:**
✅ Clear security patterns (JWT, bcrypt)
✅ Established libraries (Passport.js, Helmet)
✅ Step-by-step implementation
✅ Security testing exercises

**Risks:**
⚠️ Cryptography concepts may be unfamiliar
⚠️ OAuth flows can be confusing
⚠️ Security testing requires attacker mindset

**Mitigation:**
- Use battle-tested libraries
- Follow OWASP guidelines
- Break & Observe exercises build intuition
- Comprehensive documentation

---

**Ready to begin Module 4.**

**This is a critical module - security cannot be an afterthought. Take your time, understand the WHY behind each pattern, and test thoroughly. Security mistakes have real consequences.**


