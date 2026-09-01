Prayer Alarm — Technical Architecture

Version: 0.1.0
Status: Initial Architecture
Platforms: Android + iOS
Framework: Flutter
Language: Dart

⸻

1. Architecture Goals

The Prayer Alarm architecture must prioritize:

* Reliability
* Maintainability
* Scalability
* Testability
* Privacy
* Platform independence
* Performance
* Offline-first operation
* Easy future updates

The application must be designed so that new features can be added without rewriting existing core functionality.

⸻

2. Architectural Pattern

The application will use a modular layered architecture.

High-level structure:

Presentation
     ↓
Application / State Management
     ↓
Domain
     ↓
Data
     ↓
Platform Services

The core business logic must not depend directly on Android or iOS implementation details.

⸻

3. Project Structure

The planned Flutter project structure is:

prayer_alarm/
│
├── android/
├── ios/
├── assets/
│   ├── images/
│   ├── icons/
│   └── sounds/
│
├── lib/
│   │
│   ├── main.dart
│   │
│   ├── app/
│   │   ├── app.dart
│   │   ├── router.dart
│   │   └── theme/
│   │       ├── app_theme.dart
│   │       ├── colors.dart
│   │       ├── typography.dart
│   │       └── spacing.dart
│   │
│   ├── core/
│   │   ├── constants/
│   │   ├── errors/
│   │   ├── extensions/
│   │   ├── utils/
│   │   └── localization/
│   │
│   ├── features/
│   │   │
│   │   ├── prayer_times/
│   │   │   ├── data/
│   │   │   ├── domain/
│   │   │   └── presentation/
│   │   │
│   │   ├── alarms/
│   │   │   ├── data/
│   │   │   ├── domain/
│   │   │   └── presentation/
│   │   │
│   │   ├── verification/
│   │   │   ├── data/
│   │   │   ├── domain/
│   │   │   └── presentation/
│   │   │
│   │   ├── history/
│   │   │   ├── data/
│   │   │   ├── domain/
│   │   │   └── presentation/
│   │   │
│   │   ├── settings/
│   │   │   ├── data/
│   │   │   ├── domain/
│   │   │   └── presentation/
│   │   │
│   │   └── home/
│   │       ├── data/
│   │       ├── domain/
│   │       └── presentation/
│   │
│   └── services/
│       ├── location/
│       ├── notifications/
│       ├── camera/
│       ├── storage/
│       └── permissions/
│
├── test/
├── integration_test/
│
├── pubspec.yaml
├── analysis_options.yaml
├── README.md
│
└── docs/
    ├── PRODUCT_SPEC.md
    └── ARCHITECTURE.md

⸻

4. Feature-Based Organization

Features should be isolated.

For example:

features/
    prayer_times/
    alarms/
    verification/
    history/
    settings/

A feature must contain its own:

* Data
* Domain
* Presentation

This prevents the application from becoming a large collection of unrelated files.

⸻

5. Domain Layer

The domain layer contains business rules.

Examples:

Prayer
PrayerTime
PrayerCalculationSettings
AlarmSettings
VerificationResult
PrayerHistoryEntry
UserSettings

Domain code must not depend on Flutter UI widgets.

Domain code must not directly depend on Android APIs.

Domain code must not directly depend on iOS APIs.

⸻

6. Data Layer

The data layer handles:

* Local storage
* APIs
* External services
* Serialization
* Data repositories

Repositories should expose clean interfaces to the domain layer.

Example:

PrayerTimesRepository
AlarmRepository
VerificationRepository
HistoryRepository
SettingsRepository

The domain layer should not care whether the data comes from:

* Local storage
* Internet API
* Database
* Future cloud service

⸻

7. Platform Services

Platform-specific functionality must be isolated.

Examples:

NotificationService
AlarmService
CameraService
LocationService
PermissionService

The application should communicate with these services through interfaces.

Android-specific and iOS-specific implementations should remain isolated whenever necessary.

⸻

8. Prayer Time Engine

Prayer calculations must be treated as a core service.

Inputs may include:

Latitude
Longitude
Date
Timezone
Calculation Method
Madhab
High Latitude Rule
Asr Calculation Method

Output:

Fajr
Sunrise
Dhuhr
Asr
Maghrib
Isha

The application must support multiple calculation methods.

The calculation engine should preferably work offline after the required configuration is available.

⸻

9. Alarm Architecture

The alarm system is one of the highest-priority components.

Flow:

Prayer Calculation
       ↓
Next Prayer
       ↓
Alarm Scheduler
       ↓
Operating System
       ↓
Notification / Alarm
       ↓
Alarm Screen
       ↓
Verification
       ↓
Dismiss

The scheduler must not rely exclusively on the application remaining open.

The system must account for:

* Device restart
* Time changes
* Timezone changes
* Permission changes
* Battery optimization
* Application lifecycle
* Operating-system restrictions

⸻

10. Verification Architecture

Verification must be modular.

Initial interface:

VerificationService

Possible implementations:

FaceVerificationService
LivenessVerificationService
FutureVerificationService

The UI must not depend directly on a specific facial-recognition implementation.

This allows the verification technology to be upgraded later.

⸻

11. Privacy Architecture

Privacy must be built into the architecture.

Preferred flow:

Camera
   ↓
Temporary frame
   ↓
Local verification
   ↓
Verification result
   ↓
Temporary data discarded

The default architecture must not require uploading facial images.

The application should avoid persistent biometric storage.

⸻

12. Local Storage

The application should work without an account.

Local data may include:

* Prayer settings
* Alarm settings
* Location preferences
* Calculation method
* Prayer interaction history
* Application preferences

Storage must be abstracted behind a repository.

This allows future migration to another storage technology without rewriting the rest of the application.

⸻

13. Offline-First Principle

Core functionality should work without an internet connection whenever technically possible.

The user should still be able to:

* View prayer times
* Receive configured alarms
* Use verification
* View local history
* Change settings

Internet connectivity should not be required for basic operation.

⸻

14. State Management

Application state must be centrally managed rather than scattered across widgets.

The project will use a modern Flutter state-management solution.

The exact package will be selected during implementation based on:

* Stability
* Maintenance
* Testability
* Performance
* Community adoption
* Long-term compatibility

Business logic must remain independent of UI widgets.

⸻

15. Navigation

Navigation must use a centralized routing system.

Initial routes:

/
 /home
 /prayer
 /alarm
 /verification
 /history
 /settings

Future routes must be addable without creating tightly coupled navigation logic.

⸻

16. Dependency Injection

Services should be injected rather than instantiated throughout the application.

Bad:

SomeWidget → directly creates NotificationService

Preferred:

SomeWidget
     ↓
Application Layer
     ↓
NotificationService Interface
     ↓
Platform Implementation

This improves testing and maintainability.

⸻

17. Error Handling

Errors must be explicit.

Examples:

LocationUnavailable
NotificationPermissionDenied
CameraPermissionDenied
VerificationFailed
PrayerCalculationError
AlarmSchedulingError
StorageError

The application must provide a useful user-facing response.

Errors must not silently fail.

⸻

18. Logging

Development builds should provide structured logs.

Production builds should avoid logging sensitive information.

The application must never log:

* Facial images
* Raw biometric information
* Passwords
* Authentication tokens
* Sensitive personal information

⸻

19. Security

Security requirements:

* No secrets inside source code
* No API keys committed to GitHub
* Minimal permissions
* Secure storage for sensitive values
* Dependency updates
* Input validation
* Safe error handling

Secrets must be provided through appropriate environment/configuration mechanisms.

⸻

20. Testing Architecture

Testing will exist at multiple levels.

Unit Tests

Test:

* Prayer calculations
* Date handling
* Timezone handling
* Alarm scheduling logic
* Verification logic
* Data models

Widget Tests

Test:

* Prayer cards
* Countdown
* Alarm UI
* Verification UI
* Settings UI

Integration Tests

Test:

Prayer calculation
        ↓
Alarm scheduling
        ↓
Notification
        ↓
Verification
        ↓
History

⸻

21. CI/CD

GitHub Actions should eventually automate:

Push
 ↓
Analyze
 ↓
Format check
 ↓
Unit tests
 ↓
Widget tests
 ↓
Build
 ↓
Release pipeline

Pull requests should be validated automatically before merging.

⸻

22. Versioning

The application will use semantic versioning.

Format:

MAJOR.MINOR.PATCH

Example:

1.0.0
1.0.1
1.1.0
2.0.0

Breaking architectural or behavioral changes may require a major version.

⸻

23. Feature Flags

Future features may be controlled through feature flags.

Example:

camera_verification
advanced_statistics
cloud_sync
ai_features
family_mode

Feature flags must not become a replacement for proper architecture.

⸻

24. Performance

The application should:

* Start quickly
* Minimize background work
* Minimize battery consumption
* Avoid unnecessary network requests
* Avoid unnecessary camera processing
* Avoid memory leaks
* Use asynchronous operations where appropriate

⸻

25. Accessibility

Architecture must support:

* RTL
* Localization
* Dynamic text sizes
* Screen readers
* Semantic labels
* Keyboard/accessibility navigation where relevant

⸻

26. Internationalization

All user-visible text must be externalized.

Never hard-code user-facing text directly inside widgets.

Example:

"Next Prayer"

must eventually come from localization resources rather than being embedded directly in UI code.

⸻

27. Future Backend

The MVP should not require a backend.

However, the architecture must allow a backend to be added later.

Potential future services:

Authentication
Cloud Sync
Remote Configuration
Analytics
Subscriptions
Family Accounts
AI Services

The mobile application must remain functional if optional backend services are unavailable.

⸻

28. Database Evolution

Data models must be versionable.

Future database migrations must preserve existing user data whenever possible.

A new application version must not unnecessarily destroy local history or settings.

⸻

29. Observability

Future production releases may include optional anonymous diagnostics such as:

* Crash reports
* Performance metrics
* Alarm scheduling failures
* Feature usage statistics

Privacy must remain the priority.

⸻

30. Development Rules

Before adding a new feature, developers should ask:

1. Does this belong to an existing feature?
2. Does it introduce unnecessary coupling?
3. Can it be tested independently?
4. Does it work offline if appropriate?
5. Does it affect privacy?
6. Does it affect battery usage?
7. Does it require platform-specific implementation?
8. Can it be updated independently in the future?

⸻

31. Architecture Decision Principle

When choosing between two technical solutions, prefer the solution that provides the best balance of:

Reliability + Simplicity + Privacy + Maintainability + Scalability

Technology should serve the product.

The project must avoid unnecessary complexity, unnecessary dependencies, and unnecessary cloud infrastructure.

⸻

32. Current Status

Architecture specification completed.

Next milestone:

Create the actual Flutter application structure and configure the development toolchain.