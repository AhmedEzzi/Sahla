# Sahla Restructure - Clean Architecture with BLoC/Cubit

This project has been restructured to follow **Clean Architecture** principles with **BLoC/Cubit** for state management and **Dio** for network calls.

## Architecture Overview

The project follows Clean Architecture with three main layers:

1. **Domain Layer** - Business logic and entities (framework-independent)
2. **Data Layer** - Data sources and repository implementations
3. **Presentation Layer** - UI, BLoC/Cubit, and widgets

## Folder Structure

```
lib/
├── core/                    # Core utilities and infrastructure
│   ├── cache/              # Hive cache manager
│   ├── db/                 # Database helpers
│   ├── di/                 # Dependency injection (GetIt)
│   ├── network/            # Dio client configuration
│   ├── services/           # Core services (analytics, error tracking, etc.)
│   ├── storage/            # Storage services
│   ├── theme/              # App theming
│   └── utils/              # Utility functions
│
├── domain/                 # Domain layer (business logic)
│   ├── entities/           # Business entities
│   ├── repositories/       # Repository interfaces (abstract)
│   └── usecases/           # Use cases (business logic)
│
├── data/                   # Data layer
│   ├── datasources/        # Remote and local data sources
│   │   ├── remote/         # API data sources
│   │   └── local/          # Local storage data sources
│   ├── models/             # Data models (JSON serializable)
│   ├── repositories/       # Repository implementations
│   └── services/           # Service wrappers (OdooService, etc.)
│
└── presentation/           # Presentation layer
    ├── bloc/               # BLoC/Cubit state management
    │   └── auth/           # Auth feature BLoC
    └── pages/              # UI pages/screens
        ├── auth/           # Auth screens
        └── home/           # Home screens
```

## Completed Features

### ✅ Core Infrastructure
- [x] Dio client setup with interceptors
- [x] Database helper (Hive + SQLite)
- [x] Dependency injection (GetIt)
- [x] Core services (Analytics, Error Tracking, FCM, etc.)
- [x] Theme configuration
- [x] Environment configuration

### ✅ Auth Feature (Complete)
- [x] Domain layer:
  - [x] UserEntity
  - [x] AuthRepository interface
  - [x] LoginUseCase, LogoutUseCase, CheckAuthStatusUseCase
- [x] Data layer:
  - [x] AuthRemoteDataSource (wraps OdooService)
  - [x] AuthLocalDataSource (SharedPreferences + Database)
  - [x] AuthRepositoryImpl
- [x] Presentation layer:
  - [x] AuthCubit
  - [x] AuthState (Initial, Loading, Authenticated, Unauthenticated, Error)
  - [x] LoginScreen (needs migration from Provider to BLoC)
  - [x] InitializingScreen (needs migration from Provider to BLoC)

## Pending Features (To Be Migrated)

### 📋 PTW Feature
- [ ] Domain: PtwEntity, PtwRepository, use cases
- [ ] Data: PtwRemoteDataSource, PtwLocalDataSource, PtwRepositoryImpl
- [ ] Presentation: PtwCubit, PtwState, PTW screens

### 📋 RFID Features
- [ ] Domain: RfidEntity, RfidRepository, use cases
- [ ] Data: RfidRemoteDataSource, RfidLocalDataSource, RfidRepositoryImpl
- [ ] Presentation: RfidCubit, RfidState, RFID screens

### 📋 Transfer Feature
- [ ] Domain: TransferEntity, TransferRepository, use cases
- [ ] Data: TransferRemoteDataSource, TransferLocalDataSource, TransferRepositoryImpl
- [ ] Presentation: TransferCubit, TransferState, Transfer screens

### 📋 Store Picking Feature
- [ ] Domain: StorePickingEntity, StorePickingRepository, use cases
- [ ] Data: StorePickingRemoteDataSource, StorePickingLocalDataSource, StorePickingRepositoryImpl
- [ ] Presentation: StorePickingCubit, StorePickingState, Store Picking screens

### 📋 Batch Transfer Feature
- [ ] Domain: BatchTransferEntity, BatchTransferRepository, use cases
- [ ] Data: BatchTransferRemoteDataSource, BatchTransferLocalDataSource, BatchTransferRepositoryImpl
- [ ] Presentation: BatchTransferCubit, BatchTransferState, Batch Transfer screens

### 📋 Package Feature
- [ ] Domain: PackageEntity, PackageRepository, use cases
- [ ] Data: PackageRemoteDataSource, PackageLocalDataSource, PackageRepositoryImpl
- [ ] Presentation: PackageCubit, PackageState, Package screens

### 📋 Replenish Feature
- [ ] Domain: ReplenishEntity, ReplenishRepository, use cases
- [ ] Data: ReplenishRemoteDataSource, ReplenishLocalDataSource, ReplenishRepositoryImpl
- [ ] Presentation: ReplenishCubit, ReplenishState, Replenish screens

## Migration Pattern

For each feature, follow this pattern:

### 1. Domain Layer
```dart
// entities/feature_entity.dart
class FeatureEntity extends Equatable { ... }

// repositories/feature_repository.dart
abstract class FeatureRepository { ... }

// usecases/feature/get_feature_usecase.dart
class GetFeatureUseCase { ... }
```

### 2. Data Layer
```dart
// datasources/remote/feature_remote_datasource.dart
abstract class FeatureRemoteDataSource { ... }
class FeatureRemoteDataSourceImpl implements FeatureRemoteDataSource { ... }

// datasources/local/feature_local_datasource.dart
abstract class FeatureLocalDataSource { ... }
class FeatureLocalDataSourceImpl implements FeatureLocalDataSource { ... }

// repositories/feature_repository_impl.dart
class FeatureRepositoryImpl implements FeatureRepository { ... }
```

### 3. Presentation Layer
```dart
// bloc/feature/feature_state.dart
abstract class FeatureState extends Equatable { ... }

// bloc/feature/feature_cubit.dart
class FeatureCubit extends Cubit<FeatureState> { ... }
```

### 4. Dependency Injection
```dart
// core/di/injection_container.dart
sl.registerLazySingleton<FeatureRepository>(...);
sl.registerLazySingleton(() => GetFeatureUseCase(sl()));
sl.registerFactory(() => FeatureCubit(...));
```

## Usage Example

### Using AuthCubit in a Widget

```dart
// Login
BlocProvider.of<AuthCubit>(context).login(
  url: url,
  database: database,
  username: username,
  password: password,
);

// Listen to state
BlocBuilder<AuthCubit, AuthState>(
  builder: (context, state) {
    if (state is AuthLoading) return CircularProgressIndicator();
    if (state is AuthAuthenticated) return HomeScreen();
    if (state is AuthError) return ErrorWidget(state.message);
    return LoginScreen();
  },
)
```

## Dependencies

### State Management
- `flutter_bloc: ^8.1.6` - BLoC pattern implementation
- `equatable: ^2.0.5` - State comparison

### Dependency Injection
- `get_it: ^7.7.0` - Service locator

### Network
- `dio: ^5.5.0+1` - HTTP client
- `pretty_dio_logger: ^1.3.1` - Request logging

### Local Storage
- `shared_preferences: ^2.2.2` - Key-value storage
- `sqflite: ^2.3.0` - SQLite database
- `hive: ^2.2.3` - NoSQL database

## Next Steps

1. **Migrate LoginScreen and InitializingScreen** to use AuthCubit instead of AuthProvider
2. **Migrate remaining features** following the same pattern as Auth
3. **Update navigation** to work with BLoC state
4. **Add unit tests** for each layer
5. **Add integration tests** for features

## Notes

- The OdooService has been wrapped in AuthRemoteDataSource to maintain compatibility
- All features should follow the same clean architecture pattern
- Use Cubit for simple state management, BLoC for complex event handling
- Keep domain layer framework-independent
