# Technical Report: Enhancement of Login, Signup, and Onboarding Feature

## Executive Summary

This report documents the enhancement of the Offlyn platform's authentication and onboarding system to address critical data integrity issues that prevented users from completing account creation when switching between authentication methods. The enhanced implementation ensures that partial accounts are only created when both email and phone number are provided, eliminating authentication conflicts.

---

## 1. Background

### 1.1 Feature Scope
The Login, Signup, and Onboarding feature manages user authentication and initial account setup for the Offlyn platform, collecting essential user information including:
- Email address
- Phone number
- Name
- City

### 1.2 Authentication Methods
The platform supports two primary authentication methods:
- **Google OAuth**: Provides email address upon successful authentication
- **Phone Number OTP**: Provides phone number upon successful verification

---

## 2. Previous Implementation

### 2.1 Architecture Overview
The previous implementation followed a two-stage account creation process:

**Stage 1: Initial Authentication**
- User selects authentication method (Google or Phone OTP)
- Upon successful authentication, a partial account is created with:
  - Email address (Google login), OR
  - Phone number (Phone OTP login)
- Existing complete accounts trigger immediate login

**Stage 2: Onboarding**
- User proceeds through onboarding steps to provide missing information
- Upon completion, partial account transitions to complete account

### 2.2 Authentication Flow
```
User Action → Authentication → Check Database → Result
├─ Google Login → Email Retrieved → Existing Account? → Login
│                                  └─ No → Create Partial Account (Email only)
└─ Phone OTP → Phone Retrieved → Existing Account? → Login
                                └─ No → Create Partial Account (Phone only)
```

---

## 3. Limitations of Previous Implementation

### 3.1 Primary Issue: Duplicate Partial Accounts
The previous implementation suffered from a critical flaw when users switched authentication methods between sessions:

**Scenario 1: Google-First Authentication**
1. User signs up with Google (partial account created with email)
2. User abandons onboarding before providing phone number
3. User returns later and attempts Phone OTP signup
4. New partial account created with phone number
5. During onboarding, user attempts to enter email address
6. **Error**: Email already exists in database (from previous partial account)
7. **Result**: User unable to complete onboarding

**Scenario 2: Phone-First Authentication**
1. User signs up with Phone OTP (partial account created with phone)
2. User abandons onboarding before providing email
3. User returns later and attempts Google signup
4. **Error**: Phone number already exists in database
5. **Result**: User unable to complete onboarding

### 3.2 Database Integrity Issues
- Multiple orphaned partial accounts per user
- No mechanism to reconcile partial accounts with same user identity
- Database constraints preventing account completion
- Poor user experience with unexplained authentication failures

### 3.3 User Impact
- Account creation failures without clear error messaging
- Users locked out from completing registration
- Support burden from confused users
- Potential user churn during critical onboarding phase

---

## 4. Enhanced Implementation

### 4.1 Core Principle
**Partial accounts are created only when both email AND phone number are available.**

### 4.2 New Authentication Flow
```
User Action → Authentication → Data Collection → Account Creation
├─ Google Login → Email Retrieved → Prompt for Phone → Both Present? → Create Partial Account
│                                                     └─ No → Store in Session Only
└─ Phone OTP → Phone Retrieved → Prompt for Email → Both Present? → Create Partial Account
                                                    └─ No → Store in Session Only
```

### 4.3 Implementation Details

**Phase 1: Authentication**
- User authenticates via Google or Phone OTP
- Retrieved credential (email or phone) stored in session, NOT database
- No database write operation occurs at this stage

**Phase 2: Credential Collection**
- User prompted to provide complementary credential:
  - Google users: Enter phone number
  - Phone OTP users: Enter email address
- System validates both credentials are present

**Phase 3: Partial Account Creation**
- Only when BOTH email and phone number are collected:
  - Partial account created in database
  - User proceeds to onboarding for remaining details (name, city)

**Phase 4: Account Completion**
- User completes onboarding steps
- Account status updated to "complete"

### 4.4 Technical Advantages
1. **Single Source of Truth**: Each user has at most one partial account
2. **Authentication Method Agnostic**: Users can authenticate via either method regardless of initial signup method
3. **Data Integrity**: No orphaned single-credential accounts in database
4. **Cleaner Database Schema**: Eliminated need for NULL email or phone fields in partial accounts

---

## 5. Problem Resolution Analysis

### 5.1 How Enhancement Addresses Core Issues

**Issue**: Duplicate partial accounts with different credentials
- **Resolution**: Partial accounts only created with both email AND phone, preventing duplicates

**Issue**: Users unable to complete onboarding when switching authentication methods
- **Resolution**: Since all partial accounts contain both credentials, authentication method becomes irrelevant for existing accounts

**Issue**: Database constraint violations during onboarding
- **Resolution**: No partial accounts exist with only one credential, eliminating constraint conflicts

### 5.2 User Experience Improvements
- Seamless authentication regardless of method chosen
- Reduced onboarding friction
- Elimination of cryptic error messages
- Consistent behavior across authentication methods

---

## 6. Limitations and Considerations of Enhanced Implementation

### 6.1 Data Migration Requirements

**Challenge**: Existing partial accounts in database contain only email OR phone number

**Required Actions**:

1. **Migration Strategy Options**:
   - **Option A - Deletion**: Remove partial accounts with user notification and archive them for promotions.
   - **Option B - Manual Reconciliation**: Contact users to provide missing credential
   - **Option C - Soft Migration**: Mark old partial accounts as "legacy" and handle separately in authentication logic

**Recommended Approach**: Option C is a suitable option as it prevents user friction and deprecation of existing logic for weekly promotions. Suggest Option A for partial account that are too old for preventing inflated user count.

### 6.2 Backward Compatibility

**Challenge**: Existing mobile/web applications in production expect old API behavior

### 6.3 Legacy Account Compatibility

**Challenge**: Partial accounts created under old model must function with new authentication logic

### 6.4 Data Loss Risk

**Challenge**: User-provided credentials stored in memory (not database) until both are collected

### 6.5 User Experience Friction

**Challenge**: Users now must provide both credentials before any data is saved

**Potential User Concerns**:
1. **"I already gave you my email"** - Users authenticated with Google expect email to be saved
2. **Perceived extra steps** - Additional input field may feel like more work
3. **Trust issues** - Some users uncomfortable providing phone after Google login

---

**Document Version**: 1.0  
**Last Updated**: October 27, 2025  
**Author**: Maqsud Mallick
