# 🍔 Food Delivery Authentication & Authorization Database Schema (RBAC)

> A scalable, production-ready, and highly optimized database schema designed for multi-tenant Food Delivery applications (e.g., Talabat).

---

## 🗺️ Database ERD Diagram

![Database Schema Diagram](https://i.postimg.cc/Bn4z1vJj/talabat.png)


---



## 📑 Use Cases, Architectural Blueprints & System Diagrams

Each Use Case details the underlying system triggers, pre/postconditions, atomic execution vectors, automated failure exceptions, structural blueprints (Pure Java Pseudocode), and reactive operational diagrams (Sequence & Activity models).

---

### 1️⃣ Use Case Name: Sign Up (Local Form Registration)
* **Actor(s):** Customer (Potential User)
* **Goal:** Register a new user and prepare a disabled central identity record .
* **Trigger:** User fills the registration form and clicks "Sign Up".
* **Preconditions:** The Email or Phone does not exist in the central user storage .

#### 🛠️ Execution Flows
##### Main Flow:
- **Actor(s):** Customer (Potential User)
- **Goal:** **Register a new user and prepare a disabled central identity record**
- **Trigger:** User fills the registration form and clicks "Sign Up".
- **Preconditions:** The Email or Phone does not exist in the central user storage 

**Main Flow:**

1. The app triggers a registration operation passing email, phone, and password 
2. The system checks if the email or phone is already taken (Look up in the central `users` storage) & Passwords must be at least 8 characters long & required field are filled .
3. System creates a new identity record with a unique identifier (`UUID`) .
4. System hashes the password string using a simulated delay/hash function.
5. System sets the account state to inactive (`is_enabled = false`) .
6. System creates a mapping between the new `UUID` and `ROLE_CUSTOMER`.
7. System generates a temporary 6-digit OTP code string then send it through Email or Phone.
8. System stores the OTP code in a simulated cache (`Map`) with a simulated expiration timestamp.
9. System prints a simulation message showing the sent OTP and yields a "Pending Verification" state.
    
- **Alternate Flow(s):**
    - A1 (Phone-Only Signup): User registers via Phone only. System skips the email property, saves `password_hash = NULL`, and proceeds directly to generate the OTP.
    
- **Exceptions:**
    - E1 (Identity Already Registered): If the look-up finds the email or phone, the operation drops immediately and outputs "Error: Identity Conflict" .
#### 🏁 System Boundaries
- **Postconditions:** A locked user identity is written to memory, and an active verification token is waiting in the cache storage.
- **Business Rules:** Accounts remain locked until OTP validation succeeds.
- **Nonfunctional Requirements:** Plaintext passwords must never be stored directly in memory or output logs.

---

####  📝 Pseudocode

```text
FUNCTION signUp(email, phone, rawPassword):
    IF email OR phone already exists in usersTable:
        RETURN "Error: Identity Conflict"
    
    CREATE new userId = generateUUID()
    CREATE passwordHash = simulatedBCrypt(rawPassword)
    
    INSERT INTO usersTable (userId, email, phone, passwordHash, provider='LOCAL', type='CUSTOMER', isEnabled=false)
    INSERT INTO userRolesTable (userId, role='ROLE_CUSTOMER')
    
    CREATE otpCode = generate6DigitRandomString()
    INSERT INTO otpCache (phone_or_email, otpCode)
    
    PRINT "SMS Sent with OTP"
    RETURN "Pending Verification"
```


#### 💻 Pure Java Pseudocode 
```java
// Simulated Business Execution Logic inside the Core Auth Module
public static String signUp(String email, String phone, String rawPassword) {
    // Step 1: Duplicate check across the live in-memory table
    for (UserRow u : usersTable.values()) {
        if ((email != null && email.equals(u.email)) || (phone != null && phone.equals(u.phone))) {
            return "Error: Identity Conflict";
        }
    }
    
    // Step 2: Hashing and Identity provisioning
    String userId = "UUID-" + java.util.UUID.randomUUID().toString();
    String passwordHash = bCryptSimulatedEncoder(rawPassword);
    
    UserRow newUser = new UserRow(userId, email, phone, passwordHash, "LOCAL", "CUSTOMER", false);
    usersTable.put(userId, newUser);
    userRolesTable.put(userId, "ROLE_CUSTOMER");
    
    // Step 3: Volatile OTP code distribution mapping
    String otpCode = generateRandom6DigitString();
    String targetKey = (phone != null) ? phone : email;
    otpCache.put(targetKey, otpCode);
    
    smsGatewayEmitter.send(targetKey, "Your validation code is: " + otpCode);
    return "Pending Verification";
}
```

---

#### 📊 Sequence Diagram (Architecture Lifecyle)

```mermaid
sequenceDiagram
    autonumber
    actor Customer as 👤 Customer
    participant App as 📱 App
    participant Auth as 🟢 Auth Service
    participant DB as 🗄️ Users Store
    participant Redis as ⚡ OTP Cache
    participant Notify as ✉️ Notification Service

    Customer->>App: Fill registration form (email, phone, password)
    App->>Customer: Click "Sign Up"
    App->>Auth: registerUser(email, phone, password)
    Note over Auth: Validate password strength
    Note over Auth: Validate required fields filled
    Auth->>DB: Look up email or phone
    
    alt Identity already exists (Exception E1)
        DB-->>Auth: Match found
        Auth-->>App: Error: Identity Conflict
        App-->>Customer: Show "Identity Conflict" error
    else Identity available (Main Flow)
        DB-->>Auth: No match found
        Note over Auth: Generate UUID
        Note over Auth: Hash password (Simulated delay)
        Auth->>DB: Create identity record (UUID, email, phone, password_hash, is_enabled=false)
        Auth->>DB: Map UUID to ROLE_CUSTOMER
        Note over Auth: Generate 6-digit OTP code
        Auth->>Redis: Store OTP (UUID, code, expires_at)
        Auth->>Notify: Send OTP via Email or Phone
        Notify-->>Customer: Deliver OTP code
        Auth-->>Auth: Print simulation message (OTP Sent)
        Auth-->>App: Return "Pending Verification" state
        App-->>Customer: Show "Pending Verification" screen
    end
```


---

#### 📊 Activity Diagram (Workflow Logic Control)

```mermaid
stateDiagram-v2
    [*] --> FillRegistrationForm : User fills details
    FillRegistrationForm --> SubmitRequest : Click "Sign Up"
    
    state SubmitRequest <<choice>>
    SubmitRequest --> ValidateFields : Check fields structure
    
    state CheckUnique <<choice>>
    ValidateFields --> CheckUnique : Query SQL users table
    
    CheckUnique --> RejectConflict : If Email/Phone Exists
    RejectConflict --> [*] : Terminate Transaction (HTTP 409)
    
    CheckUnique --> ProcessSignup : If Identity is Unique
    state ProcessSignup {
        [*] --> HashPassword : Run BCrypt
        HashPassword --> SaveUserIdentity : Insert into 'users' (isEnabled=false)
        SaveUserIdentity --> MapRole : Insert into 'user_roles' (ROLE_CUSTOMER)
        MapRole --> CacheOTP : Write code to Redis (TTL 120s)
        CacheOTP --> SendSMS : Fire Notification Gateway
    }
    
    ProcessSignup --> ShowOTPScreen : HTTP 201 Created Status
    ShowOTPScreen --> [*] : User enters OTP Verification state
```
📊 Flow chart

![Database Schema Diagram](https://i.postimg.cc/RVfn3cRW/signup.png)

---

## 2️⃣ Use Case Name: OTP Verification

- **Actor(s):** Customer
- **Goal:** **Validate the temporary token, activate the identity record, and provision the dynamic profile (<u>insert the active user in CustomerProfile table</u>).**
- **Trigger:** User enters the 6-digit OTP code.
- **Preconditions:** A user record exists with `is_enabled = false` & its OTP code saved in the memory (ex: Redis).

**Main Flow:**

1. User submits the account identifier (Email/Phone) and the verification code.
2. System looks up the identifier in the temporary OTP cache storage.
3. System verifies that the submitted code exactly matches the cached code string.
4. System updates the target user identity record, changing `is_enabled` to `true`.
5. On-the-Fly Profile Injection: System performs a search in the customer profiles storage. Finding nothing, it writes a brand new row containing default starting parameters (`points = 0`, `tier = 'BRONZE'`).
6. System purges the used OTP string from the cache storage.
7. System generates an active **Access Token and Refresh Token** session.
8. System outputs a dynamic confirmation payload with the new credentials.

- **Alternate Flow(s):** None 
- **Exceptions:**
    - E1 (Invalid/Expired Token): If the code is missing from the cache or doesn't match, the system drops the operation and returns "Error: Invalid OTP Verification".
- **Postconditions:** The core user identity is unlocked and a fresh business functional profile is safely initialized.
- **Business Rules:** Verified OTP tokens are instantly destroyed. Customer has maximum 3 times to activate his account in each 1 hour.
- **Nonfunctional Requirements:** Execution speed should be direct memory access (O(1) complexity lookup) 

## 📝 Pseudocode:

```text
FUNCTION verifyOTP(identifier, submittedCode):
    GET cachedCode FROM otpCache using identifier
    IF cachedCode IS NULL OR cachedCode != submittedCode:
        RETURN "Error: Invalid OTP Verification"
    
    FIND user in usersTable where email==identifier OR phone==identifier
    SET user.isEnabled = true
    
    IF customerProfilesTable DOES NOT contain user.id:
        INSERT INTO customerProfilesTable (userId=user.id, points=0, tier='BRONZE')
        
    DELETE identifier FROM otpCache
    RETURN "Tokens Generated: Access & Refresh"
```


#### 📊 Sequence Diagram: OTP Verification
```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 Customer
    participant App as 📱 App UI
    participant Auth as 🟢 Auth Service
    participant Redis as ⚡ Redis Cache (otpCache)
    participant DB as 🗄️ SQL DB

    User->>App: Enters 6-digit OTP code
    App->>Auth: POST /api/v1/auth/verify-otp (Identifier, Code)
    
    critical Rate Limit Assessment
        Note over Auth: Ensure user has <= 3 attempts per hour
    end

    Auth->>Redis: Look up code using Identifier
    Redis-->>Auth: Return cached code string
    
    alt Code missing or doesn't match (Exception E1)
        Auth-->>App: Return "Error: Invalid OTP Verification"
        App-->>User: Show validation failure error
    else Code matches perfectly (Main Flow)
        Auth->>DB: UPDATE users SET is_enabled=true WHERE identifier=?
        Auth->>DB: SELECT * FROM customer_profiles WHERE user_id=?
        
        Note over Auth: Profile Injection Routine
        Auth->>DB: INSERT INTO customer_profiles (user_id, points=0, tier='BRONZE')
        Auth->>Redis: DEL otp_code_key
        Note over Auth: Generate active Access & Refresh Tokens
        Auth-->>App: HTTP 200 OK (Tokens + Confirmation Payload)
        App-->>User: Grant landing app entry
    end
```

#### 📊 Activity Diagram: OTP Verification
```mermaid
stateDiagram-v2
    [*] --> CheckAttempts : User inputs OTP Code
    
    state RequestEvaluation <<choice>>
    CheckAttempts --> RequestEvaluation : Verify attempt history <= 3/hr
    RequestEvaluation --> RejectSpam : Attempts > 3
    RejectSpam --> [*] : Block for 1 hour (HTTP 429)
    
    RequestEvaluation --> QueryRedis : Attempts <= 3
    
    state ValidateCode <<choice>>
    QueryRedis --> ValidateCode : Check code vs Redis Cache
    ValidateCode --> RaiseError : Code incorrect / Expired
    RaiseError --> [*] : Invalidate Request (HTTP 400)
    
    ValidateCode --> UnlockAccount : Code matches
    state UnlockAccount {
        [*] --> ActivateUser : Set is_enabled = true
        ActivateUser --> ScanProfiles : Look up user_id in profiles
        ScanProfiles --> InsertRow : Record Absent (On-the-fly)
        InsertRow --> ClearCache : Flush Redis OTP Key
        ClearCache --> BuildTokens : Generate JWT Token Pair
    }
    
    UnlockAccount --> RedirectToHome : HTTP 200 Success
    RedirectToHome --> [*] : Active Session Initialized
```
![Database Schema Diagram](https://i.postimg.cc/V66tZvCq/otp.png)

---

## 3️⃣ Use Case Name: Login (Traditional Authentication)

- **Actor(s):** Customer
- **Goal:** Authenticate identity credentials and return dynamic platform tokens.
- **Trigger:** User enters Email/Phone and Password 
- **Preconditions:** The user record must exist and be active (`is_enabled = true`) 

**Main Flow:**

1. User submits login inputs along with an explicit header tag (`X-App-Client = Customer`).
2. System performs a lookup on the central user database map via the unique identifier 
3. System matches the raw password string against the stored hashed password string.
4. System verifies that the user record holds the appropriate access role (`ROLE_CUSTOMER`) 
5. System accesses the customer profiles table using the unique `user_id` to pull dynamic traits (Points, Tiers) 
6. System creates a simulated secure session block containing the identifier and roles.
7. System stores the session reference in a simulated session whitelist cache.
8. System yields an authenticated success state and hands over the token block.

- **Alternate Flow(s):**
    - A1 (Rider enters Customer App): A user with `ROLE_RIDER` tries logging into this portal  The system performs the On-the-Fly Profile Migration: instantly inserts a row into `customer_profiles`, appends `ROLE_CUSTOMER` to the user's role mapping, and safely lets him view customer features 
- **Exceptions:**
    - E1 (Invalid Credentials): Wrong password or email yields a generic "Error: Authentication Failed" output without telling which field is incorrect.
    - E2 (Banned/Disabled Account): If the account is locked (`is_enabled = false`), system halts and returns "Error: Account Suspended".
- **Postconditions:** An active operational session token is saved in memory.

## 📝 Pseudocode:

```text
FUNCTION login(identifier, rawPassword, appHeader):
    FIND user in usersTable using identifier
    IF user IS NULL OR user.passwordHash != hashed(rawPassword):
        RETURN "Error: Authentication Failed"
        
    IF user.isEnabled IS false:
        RETURN "Error: Account Suspended"
        
    IF appHeader == "Customer" AND user.roles does not have ROLE_CUSTOMER:
        //  On-the-fly Profile Migration 
        INSERT ROLE_CUSTOMER to user
        INSERT customer_profile row
        
    GET profile FROM customerProfilesTable using user.id
    CREATE sessionKey = "SESSION-" + user.id
    INSERT INTO sessionWhitelist(sessionKey, true)
    
    RETURN "Login Success with Profile Data"
```


#### 📊 Sequence Diagram: Traditional Login
```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 Customer Client
    participant App as 📱 Customer App
    participant GW as 🛡️ API Gateway / Auth
    participant DB as 🗄️ SQL DB

    User->>App: Input Email/Phone & Password
    App->>GW: POST /api/v1/auth/login (Payload + X-App-Client Header)
    GW->>DB: SELECT * FROM users WHERE email=? OR phone=?
    
    alt User not found / Bad Password (Exception E1)
        DB-->>GW: Record not found / mismatch hash
        GW-->>App: HTTP 401 Unauthorized (Error: Authentication Failed)
        App-->>User: Show generic credentials validation error
    else User exists but is suspended (Exception E2)
        DB-->>GW: Record located (is_enabled = false)
        GW-->>App: HTTP 403 Forbidden (Error: Account Suspended)
        App-->>User: Display account suspension alert
    else Identity Verified (Main Flow / Alternate Match)
        Note over GW: Validate password string vs stored BCrypt hash
        
        alt App Client Header is "Customer" but Role is ONLY RIDER (Alternate Flow A1)
            Note over GW: Dynamic Profile Migration
            GW->>DB: INSERT INTO customer_profiles (user_id, points=0, tier='BRONZE')
            GW->>DB: INSERT INTO user_roles (user_id, role_name='ROLE_CUSTOMER')
        end

        GW->>DB: SELECT points, loyalty_tier FROM customer_profiles WHERE user_id=?
        DB-->>GW: Return profile traits data
        Note over GW: Construct signed JWT token containing Roles
        Note over GW: Store session identifier in Whitelist Cache
        GW-->>App: HTTP 200 OK (JWT Access & Refresh + Profile payload)
        App-->>User: Present dynamic application Home screen
    end
```

#### 📊 Activity Diagram: Traditional Login
```mermaid
stateDiagram-v2
    [*] --> RetrieveUser : User submits Form inputs
    RetrieveUser --> VerifyCredentials : Fetch record from SQL Map
    
    state CredentialCheck <<choice>>
    VerifyCredentials --> CredentialCheck : Match BCrypt hash string
    CredentialCheck --> DropAuth : Credentials Invalid
    DropAuth --> [*] : Halt and return HTTP 401
    
    CredentialCheck --> CheckEnabledStatus : Credentials Valid
    
    state StatusCheck <<choice>>
    CheckEnabledStatus --> StatusCheck : Read is_enabled property
    StatusCheck --> DropSuspended : is_enabled == false
    DropSuspended --> [*] : Halt and return HTTP 403
    
    StatusCheck --> InterceptHeader : is_enabled == true
    
    state HeaderPolicy <<choice>>
    InterceptHeader --> HeaderPolicy : Inspect X-App-Client == Customer
    HeaderPolicy --> OnTheFlyMigration : Role is only RIDER
    OnTheFlyMigration --> FinalizeLogin : Inject profile and update role mapping
    
    HeaderPolicy --> FinalizeLogin : Role already contains CUSTOMER
    
    state FinalizeLogin {
        [*] --> FetchProfileData : Load Points and Loyalty Tier
        FetchProfileData --> RegisterActiveSession : Add token reference to Whitelist
        RegisterActiveSession --> OutputTokenBlock : Package payload response
    }
    FinalizeLogin --> [*] : Grant User Platform Entry (HTTP 200)
```
```mermaid
graph TD
    Start([Start]) --> Input[User submits Email/Phone and Password and Header]
    Input --> Lookup[Lookup user in central User DB by unique identifier]
    Lookup --> CheckExists{User exists?}

    %% Exception Flow 1
    CheckExists -- No --> Err1[E1: Authentication Failed Generic error]
    Err1 --> EndFail([End Fail])

    %% Main Flow
    CheckExists -- Yes --> CheckEnabled{is_enabled == true?}
    
    %% Exception Flow 2
    CheckEnabled -- No --> Err2[E2: Account Suspended is_enabled = false]
    Err2 --> EndFail

    %% Main Flow
    CheckEnabled -- Yes --> CheckPassword{Password matches hashed password?}
    
    %% Exception Flow 3
    CheckPassword -- No --> Err3[E1: Authentication Failed Wrong password]
    Err3 --> EndFail

    %% Main Flow
    CheckPassword -- Yes --> CheckRole{Has ROLE_CUSTOMER?}
    
    %% Alternate Flow A1
    CheckRole -- No ROLE_RIDER --> Migration[On-the-Fly Profile Migration A1: Insert row and Append Role]
    Migration --> FetchProfile
    
    %% Main Flow Final Loops
    CheckRole -- Yes --> FetchProfile[Fetch customer_profiles by user_id and Load Points]
    FetchProfile --> CreateSession[Create secure session block identifier and roles]
    CreateSession --> StoreWhitelist[Store session ref in whitelist cache]
    StoreWhitelist --> ReturnTokens[Return authenticated success and token block]
    ReturnTokens --> EndSuccess([End Success])
```

---

## 4️⃣ Use Case Name: Social Media Authentication

- **Actor(s):** Customer
- **Goal:** Authenticate via external major identity token flows seamlessly.
- **Trigger:** User clicks "Continue with Google" or "Facebook" 
- **Preconditions:** The mobile frontend has acquired a valid external IdP provider token.

**Main Flow:**

1. Frontend submits the provider name (`GOOGLE`), the identity token, and a category dropdown tag (`intent = CUSTOMER`) 
2. System parses the social payload signature and extracts the account attributes (`email`, `provider_id`, names).
3. System looks up the email or `provider_id` in the central user storage.
4. Scenario A (New Social Sign-Up): If the user doesn't exist, the system creates an automated identity record (`is_enabled = true`, `auth_provider = 'GOOGLE'`), links `ROLE_CUSTOMER`, and immediately inserts a profile row into `customer_profiles` 
5. Scenario B (Returning Social User): If found, the registration step is skipped.
6. System initiates an active session payload for the user.
7. System returns a successful authentication output.

- **Alternate Flow(s):** None (Keep it basic).
- **Exceptions:**
    - E1 (Forged Social Identity): If the provider token verification breaks down, system returns "Error: Unverified Social Provider".
- **Postconditions:** A user profile is bound to the platform identity, allowing direct token generation.

## 📝 Pseudocode:

```text
FUNCTION socialLogin(socialEmail, providerId, intent):
    FIND user in usersTable using socialEmail OR providerId
    
    IF user IS NOT FOUND: // مستخد
        CREATE userId = generateUUID()
        INSERT INTO usersTable (userId, socialEmail, providerId, provider='GOOGLE', isEnabled=true)
        INSERT INTO userRolesTable (userId, role='ROLE_CUSTOMER')
        INSERT INTO customerProfilesTable (userId, points=0, tier='BRONZE')
        
    CREATE sessionKey = "SESSION-" + user.id
    INSERT INTO sessionWhitelist(sessionKey, true)
    RETURN "Social Auth Success"
```

#### 📊 Sequence Diagram: Social Authentication
```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 Customer Client
    participant App as 📱 Mobile Frontend SDK
    participant GW as 🛡️ API Gateway / Auth
    participant Provider as 🌐 External IdP (Google/FB)
    participant DB as 🗄️ SQL DB

    User->>App: Clicks "Continue with Google"
    App->>Provider: Authenticate and request Identity Token
    Provider-->>App: Hand over cryptographic Third-Party Token
    App->>GW: POST /api/v1/auth/social (Provider, Token, intent)
    GW->>Provider: Validate incoming token signature against JWKS Public Keys
    
    alt Signature check fails (Exception E1)
        Provider-->>GW: Unverified signature or expired token
        GW-->>App: HTTP 401 Unauthorized (Error: Unverified Social Provider)
        App-->>User: Display social integration error message
    else Signature verified safely
        Provider-->>GW: Extract metadata (email, provider_id, name)
        GW->>DB: SELECT * FROM users WHERE email=? OR provider_id=?
        
        alt Scenario A: New Social Sign-Up (User Absent)
            DB-->>GW: Record not found
            GW->>DB: INSERT INTO users (id, email, provider_id, provider, is_enabled=true)
            GW->>DB: INSERT INTO user_roles (user_id, role_name)
            GW->>DB: INSERT INTO customer_profiles (user_id, points, loyalty_tier)
        else Scenario B: Returning Social User (User Located)
            DB-->>GW: Return verified user identity rows
            Note over GW: Bypass structural profile creation steps
        end
        
        Note over GW: Construct application dynamic Token payload
        GW-->>App: HTTP 200 OK (Platform Access Token + Profile state)
        App-->>User: Unlock core application entry dashboard
    end
```

#### 📊 Activity Diagram: Social Authentication
```mermaid
graph TD
    Start([👤 User chooses Social Provider]) --> ContactGW[Dispatch token payload to Auth Gateway]
    ContactGW --> VerifySig{Verify Signature against JWKS}
    
    %% Exception Flow
    VerifySig -- Corrupted / Expired --> FailVal[Signature Corrupted]
    FailVal --> EndFail([Reject Client Query: HTTP 401 Error])
    
    %% Main Flow
    VerifySig -- Valid --> ReadStore[Search email or provider_id in DB]
    ReadStore --> AccountMatch{Is User Registered?}
    
    %% Scenario A
    AccountMatch -- No: Scenario A --> AutoReg[Auto Register User: is_enabled=true]
    AutoReg --> AssignRole[Map role_name = ROLE_CUSTOMER]
    AssignRole --> ProvisionProf[Insert baseline customer_profile]
    ProvisionProf --> AssembleSession
    
    %% Scenario B
    AccountMatch -- Yes: Scenario B --> SkipReg[Skip Registration Loops]
    SkipReg --> AssembleSession
    
    %% Final Block
    AssembleSession[Compile Access & Refresh Tokens JWT] --> FlushResp[Return Access Tokens Payload]
    FlushResp --> EndSuccess([Open Platform Dashboard: HTTP 200])
```

---

## 5️⃣ Use Case Name: User / Customer Profile (Data Access)

- **Actor(s):** Customer
- **Goal:** Retrieve unified business profile configurations safely without security leaks 
- **Trigger:** User navigates to the "Account Info" interface page 
- **Preconditions:** The request supplies a valid, non-expired token string 

**Main Flow:**

1. App executes a fetch request passing the active token.
2. System extracts the `userId` claim directly from the token string.
3. System reads from both the central `users` map and the relational `customer_profiles` map by matching the shared key (`user_id`) 
4. System joins the security details (Email/Phone) with business metrics (Loyalty Points, Tier) 
5. System outputs a clean, combined view payload to the user screen.

- **Alternate Flow(s):** None.
- **Exceptions:**
    - E1 (Token Terminated/Expired): If the token signature is missing or lifespan window is closed, system yields "Error: Session Expired".
- **Postconditions:** Accurate system profile data is delivered safely.

## 📝 Pseudocode:

```text
FUNCTION getProfileData(tokenKey):
    IF tokenKey exists in tokenBlocklist:
        RETURN "Error: Session Expired"
        
    EXTRACT userId from tokenKey
    GET user FROM usersTable using userId
    GET profile FROM customerProfilesTable using userId
    
    RETURN combined_view(user.email, user.phone, profile.points, profile.tier)
```


#### 📊 Sequence Diagram: User Profile Data Access
```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 Customer Client
    participant App as 📱 Customer App UI
    participant GW as 🛡️ API Gateway / Interceptor
    participant DB as 🗄️ SQL Database Shared Grid

    User->>App: Navigates to "Account Info" screen
    App->>GW: GET /api/v1/profiles/me (Header: Bearer AccessToken)
    Note over GW: Parse JWT token and check signature authenticity
    
    alt Token signature invalid or expired (Exception E1)
        GW-->>App: HTTP 401 Unauthorized (Error: Session Expired)
        App-->>User: Route back to login screen / challenge refresh
    else Token verification passes cleanly
        Note over GW: Extract target user_id (UUID) from Token claims
        GW->>DB: SELECT records from users and customer_profiles
        DB-->>GW: Return combined core structural and business rows
        GW-->>App: HTTP 200 OK (Aggregated Presentation Payload)
        App-->>User: Display unified account attributes on UI
    end
```

#### 📊 Activity Diagram: User Profile Data Access
```mermaid
graph TD
    Start([👤 User opens Account Info Screen]) --> InterceptJWT[Read Authorization Bearer Token Header]
    InterceptJWT --> TokenValidation{Validate JWT Signature & Lifespan}
    
    %% Exception Flow
    TokenValidation -- Forged / Expired --> RejectCall[Signature invalid or expired]
    RejectCall --> EndFail([Force system logout: HTTP 401 Response])
    
    %% Main Flow
    TokenValidation -- Valid --> UnpackClaim[Extract unique user_id UUID from claims]
    UnpackClaim --> JoinQuery[Execute Shared Table Lookup Query]
    JoinQuery --> BindDTO[Bind central credentials metadata with business metrics]
    BindDTO --> RenderUI[Render aggregated DTO properties package back to UI]
    RenderUI --> EndSuccess([Account properties successfully visible on screen])
```
---


## 6️⃣ Use Case Name: Forget Password

- **Actor(s):** Customer
- **Goal:** Establish a secure recovery loop to modify lost passwords.
- **Trigger:** User inputs their account address and hits "Forgot Password".
- **Preconditions:** The account identifier must link to an active registered local profile.

**Main Flow:**

1. User triggers the password recovery flow with an email/phone input.
2. System checks the database map. If found, it clears old pending reset codes.
3. System creates a dynamic high-entropy reset token string.
4. System registers the token in a temporary recovery storage map with a short lifespan countdown.
5. System fires an simulated notification gateway showing the password reset path.
6. System prints a successful setup notification.

- **Alternate Flow(s):** None.
- **Exceptions:**
    - E1 (Social Hashing Conflict): If the account was initialized via `GOOGLE`, it lacks a password property. System returns "Error: Please authenticate using your Google login portal".
- **Postconditions:** A temporary recovery session token is logged in memory.

## 📝 Pseudocode:

```text
FUNCTION forgetPassword(identifier):
    FIND user in usersTable using identifier
    // Protect from Account Enume
    IF user IS NOT FOUND:
        PRINT "Simulated Notification Sent"
        RETURN "Success" 
        
    IF user.provider == "GOOGLE":
        RETURN "Error: Please login via Google"
        
    CREATE resetToken = generateUUID()
    INSERT INTO otpCache("RESET-" + identifier, resetToken)
    PRINT "Notification Gateway: Sent Reset URL"
    RETURN "Success"
```

#### 📊 Sequence Diagram: Forget Password Workflow
```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 Customer Client
    participant App as 📱 Customer App UI
    participant GW as 🛡️ Auth Recovery Module
    participant DB as 🗄️ SQL DB Table Map
    participant Redis as ⚡ Redis (otpCache Mapping)
    participant Notify as ✉️ Notification Emitter

    User->>App: Enters address identifier & hits "Forgot Password"
    App->>GW: POST /api/v1/auth/forget-password (identifier)
    GW->>DB: SELECT id, auth_provider FROM users WHERE email=? OR phone=?
    
    alt Account is unregistered / absent from system
        DB-->>GW: Record not found
        Note over GW: Prevent Account Enumeration Attacks
        GW-->>App: HTTP 200 OK (Generic success message returned blindly)
        App-->>User: Show "If your account exists, a link has been sent"
    else Account registered under Social Sign-in (Exception E1)
        DB-->>GW: Record located (auth_provider = 'GOOGLE')
        GW-->>App: HTTP 400 Bad Request (Error: Please authenticate using Google portal)
        App-->>User: Block operation and show "Use Google Account Login" banner
    else Account is Local Form standard (Main Flow)
        DB-->>GW: Record located (auth_provider = 'LOCAL')
        GW->>Redis: DEL pre-existing recovery codes for identifier
        Note over GW: Generate dynamic high-entropy reset token string
        GW->>Redis: SETEX RESET-identifier token_string (TTL: 10 mins)
        GW->>Notify: Push transaction task to notification gateway
        Notify-->>User: Receive recovery url link path via SMS/Email
        GW-->>App: HTTP 200 OK (Unified recovery status payload)
        App-->>User: Show secure confirmation confirmation alert frame
    end
```

#### 📊 Activity Diagram: Forget Password Workflow
```mermaid
graph TD
    Start([👤 User requests recovery]) --> DispatchId[Dispatch identifier string to gateway]
    DispatchId --> QueryMapping[Look up user traits records in SQL DB]
    QueryMapping --> IdentityEval{Does Account Exist?}
    
    %% Anti-Scan Flow
    IdentityEval -- No --> BlindMode[Security Anti-Scan Policy Enabled]
    BlindMode --> EndBlind([Print generic success prompt & End])
    
    %% Located Flow
    IdentityEval -- Yes --> ProviderEval{Check auth_provider Type}
    
    %% Exception E1
    ProviderEval -- GOOGLE --> RejectSocial[Bypass Social User Recovery: Exception E1]
    RejectSocial --> EndSocialFail([Drop flow & return HTTP 400 Bad Request])
    
    %% Main Flow
    ProviderEval -- LOCAL --> ProcessLocal[Evict historical reset codes from cache]
    ProcessLocal --> BuildToken[Generate high-entropy secure reset token string]
    BuildToken --> WriteCache[Write token to Redis Cache: TTL 10 mins]
    WriteCache --> FireNotify[Push transaction task to Messaging Hub]
    FireNotify --> CompleteAction[Return HTTP 200 OK Status Package]
    CompleteAction --> EndSuccess([Temporary link logged in volatile storage])
```
---
## 7️⃣ Use Case Name: Logout

- **Actor(s):** Customer
- **Goal:** Annul active tokens and terminate server-side cached sessions.
- **Trigger:** User clicks the "Log Out" button.
- **Preconditions:** The user is logged in with an active operational token.

**Main Flow:**

1. App dispatches a logout action passing the reference tokens.
2. System reads the whitelist session map and deletes the active session reference key completely.
3. Blacklisting Procedure: System isolates the Token ID (`jti`) and logs it into a simulated blocklist storage map with a strict TTL constraint.
4. System returns a successful termination message.
5. App UI flushes local application memory caches.

- **Alternate Flow(s):** None.
- **Exceptions:** None.
- **Postconditions:** The specific credentials lose validation rights globally, preventing session replays.

## 📝 Pseudocode:

```text
FUNCTION logout(tokenKey, userId):
    DELETE "SESSION-" + userId FROM sessionWhitelist
    INSERT INTO tokenBlocklist(tokenKey, true) 
    RETURN "Logged Out successfully"
```

#### 📊 Sequence Diagram: Logout Workflow
```mermaid
sequenceDiagram
    autonumber
    actor User as 👤 Customer Client
    participant App as 📱 Customer App UI
    participant GW as 🛡️ API Gateway / Auth Hub
    participant Redis as ⚡ Redis Session Cache

    User->>App: Clicks "Log Out" button
    App->>GW: POST /api/v1/auth/logout (Header: Bearer Tokens)
    
    Note over GW: Extract dynamic Session & Token ID (jti)
    GW->>Redis: DEL whitelist_session_key (Wipe live entry)
    Note over GW: Calculate remaining token lifespan window
    GW->>Redis: SETEX blocklist_jti_key true (Apply temporary TTL)
    
    GW-->>App: HTTP 200 OK (Successful termination message)
    Note over App: Flush local application memory caches
    App-->>User: Route back safely to logged-out screen
```

#### 📊 Activity Diagram: Logout Workflow
```mermaid
graph TD
    Start([👤 User triggers Logout]) --> DispatchTokens[App dispatches tokens payload to Gateway]
    DispatchTokens --> ParseJTI[Isolate Token Identifier jti & extract session markers]
    ParseJTI --> EvictWhitelist[Delete session entry key from Redis Whitelist]
    EvictWhitelist --> ApplyBlocklist[Log extracted jti into Redis Blacklist storage]
    ApplyBlocklist --> SetTTL[Apply strict structural remaining lifespan TTL constraint]
    SetTTL --> SendSuccess[Return successful termination status package: HTTP 200]
    SendSuccess --> FlushLocalMemory[Mobile Frontend flushes local storage caches]
    FlushLocalMemory --> EndProcess([Credentials globally invalidated safely])
```

---

## 8️⃣ Use Case Name: Enable / Disable Account (Governance)

- **Actor(s):** Platform Administrator / Fraud Threat Engine
- **Goal:** Enforce compliance constraints by instantly locking or unlocking sy.
- **Trigger:** An Admin updates user status, or the automatic risk tracker catches a session breach.
- **Preconditions:** The target user identity record must exist in memory.

**Main Flow:**

1. An Admin pushes a status state change query passing the target `userId` and `ena.
2. System validates that the caller owns the appropriate administrative role (`ROLE_ADMIN`).
3. System targets the specific row in the `users` table map and sets `is_ena.
4. Immediate Cache Eviction: System invokes a purge transaction that locates and wipes all live session blocks or tokens tied to this `userId` from memory instantly.
5. Subsequent calls from this identity are instantly caught and blocked.

## 📝 Pseudocode:

```text
FUNCTION administrativeBan(adminToken, targetUserId):
    IF adminToken.role != 'ROLE_ADMIN':
        RETURN "Error: Unauthorized"
        
    UPDATE usersTable SET is_enabled = false WHERE id == targetUserId
    DELETE "SESSION-" + targetUserId FROM sessionWhitelist 
    
    PRINT "Target user kicked out"
    RETURN "User Suspended"
```

- **Alternate Flow(s):** None.
- **Exceptions:**
    - E1 (Target Entity Missing): If the UUID is absent from the maps, system outputs "Error: Record Not Located".
- **Postconditions:** Platform access is revoked and all active sessions are abrupt.

#### 📊 Sequence Diagram: Administrative Governance Ban
```mermaid
sequenceDiagram
    autonumber
    actor Admin as 👑 Platform Administrator
    participant Control as 💻 Admin Dashboard Panel
    participant GW as 🛡️ Core Governance Module
    participant DB as 🗄️ SQL DB Table Map
    participant Redis as ⚡ Redis Cluster Nodes

    Admin->>Control: Select User, check "Ban" & click submit
    Control->>GW: PATCH /api/v1/admin/users/{userId}/status (enabled=false)
    Note over GW: Intercept JWT and verify caller has ROLE_ADMIN
    GW->>DB: SELECT * FROM users WHERE id = targetUserId
    
    alt Target User UUID is absent from maps (Exception E1)
        DB-->>GW: Record not located
        GW-->>Control: HTTP 404 Not Found (Error: Record Not Located)
        Control-->>Admin: Show target user missing error message
    else Target User exists safely (Main Flow)
        DB-->>GW: User record found
        GW->>DB: UPDATE users SET is_enabled = false WHERE id = targetUserId
        
        Note over GW: Immediate Cache Eviction Routine
        GW->>Redis: DEL all active sessions mapped to targetUserId
        GW->>Redis: SETEX ACCESS-TOKEN-targetUserId true (Force immediate drop)
        
        GW-->>Control: HTTP 200 OK (User Suspended confirmation)
        Control-->>Admin: Show successful platform ban update alert
    end
```

#### 📊 Activity Diagram: Administrative Governance Ban
```mermaid
graph TD
    Start([👑 Admin initiates state change query]) --> VerifyAdmin[Check caller owns ROLE_ADMIN]
    VerifyAdmin --> ReadUserStore[Query target user UUID in SQL Map]
    ReadUserStore --> UserCheck{Does User Record Exist?}
    
    %% Exception E1 Flow
    UserCheck -- No --> DropBan[Abort operation: Exception E1]
    DropBan --> EndFail([Return HTTP 404 Not Found package])
    
    %% Main Flow
    UserCheck -- Yes --> SuspendSQLRecord[Target specific row & set is_enabled = false]
    SuspendSQLRecord --> EvictActiveCache[Invoke immediate purge transaction across Redis]
    EvictActiveCache --> ClearLiveSessions[Wipe open active session blocks from memory]
    ClearLiveSessions --> BlacklistLiveTokens[Force log access tokens into blocklist grid]
    BlacklistLiveTokens --> SendConfirmation[Return successful update feedback payload: HTTP 200]
    SendConfirmation --> EndSuccess([Subsequent calls instantly caught and blocked])
```





