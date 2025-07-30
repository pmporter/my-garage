# My Garage - API Specification

## Overview

The My Garage API is built using GraphQL, providing a single endpoint for all client interactions. The API supports real-time subscriptions, file uploads, and comprehensive vehicle maintenance tracking.

**Base URL**: `https://api.my-garage.app/graphql`
**WebSocket URL**: `wss://api.my-garage.app/graphql` (for subscriptions)

## Authentication

### JWT Token Authentication
All API requests require a valid JWT token in the Authorization header:

```
Authorization: Bearer <jwt_token>
```

### Authentication Flow
1. **Login/Register**: Returns JWT access token and refresh token
2. **Access Token**: Valid for 15 minutes
3. **Refresh Token**: Valid for 30 days, used to obtain new access tokens
4. **Token Refresh**: Automatic refresh before expiration

## GraphQL Schema

### Core Types

#### User Types

```graphql
type User {
  id: ID!
  email: String!
  emailVerified: Boolean!
  name: String!
  phone: String
  avatar: String
  accountType: AccountType!
  subscriptionStatus: SubscriptionStatus!
  subscriptionExpiresAt: DateTime
  preferences: UserPreferences!
  timezone: String!
  locale: String!
  vehicles: [Vehicle!]!
  notifications: [Notification!]!
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum AccountType {
  FREE
  PREMIUM
  FLEET
}

enum SubscriptionStatus {
  ACTIVE
  CANCELLED
  EXPIRED
}

type UserPreferences {
  id: ID!
  emailNotifications: Boolean!
  pushNotifications: Boolean!
  smsNotifications: Boolean!
  maintenanceReminders: Boolean!
  warrantyExpirations: Boolean!
  partRecommendations: Boolean!
  seasonalReminders: Boolean!
  promotionalOffers: Boolean!
  maintenanceReminderDays: [Int!]!
  warrantyReminderDays: [Int!]!
  distanceUnit: DistanceUnit!
  currencyCode: String!
  dateFormat: String!
  dataSharing: Boolean!
  analytics: Boolean!
}

enum DistanceUnit {
  MILES
  KILOMETERS
}
```

#### Vehicle Types

```graphql
type Vehicle {
  id: ID!
  user: User!
  name: String!
  make: String!
  model: String!
  year: Int!
  vin: String
  licensePlate: String
  vehicleType: VehicleType!
  category: VehicleCategory!
  color: String
  mileage: Int!
  mileageUnit: DistanceUnit!
  purchaseDate: Date
  purchasePrice: Float
  purchaseMileage: Int
  currentValue: Float
  status: VehicleStatus!
  location: String
  specifications: VehicleSpecs!
  lastServiceDate: Date
  lastServiceMileage: Int
  nextServiceDue: Date
  nextServiceMileage: Int
  healthScore: Int
  primaryPhotoUrl: String
  photos: [Photo!]!
  documents: [Document!]!
  maintenanceRecords: [MaintenanceRecord!]!
  maintenanceSchedule: MaintenanceSchedule!
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum VehicleType {
  MOTORCYCLE
  CAR
  TRUCK
  SUV
  BOAT
  BICYCLE
  ATV
  JETSKI
  RV
  TRAILER
  EQUIPMENT
  OTHER
}

enum VehicleCategory {
  # Motorcycles
  SPORT_BIKE
  CRUISER
  TOURING
  ADVENTURE
  DIRT_BIKE
  SCOOTER
  
  # Cars
  SEDAN
  HATCHBACK
  COUPE
  CONVERTIBLE
  WAGON
  
  # Trucks/SUVs
  PICKUP
  COMPACT_SUV
  MIDSIZE_SUV
  FULL_SIZE_SUV
  
  # Boats
  PONTOON
  BASS_BOAT
  SAILBOAT
  YACHT
  
  # Other
  ROAD_BIKE
  MOUNTAIN_BIKE
  OTHER
}

enum VehicleStatus {
  ACTIVE
  SOLD
  TOTALED
  STORED
}

type VehicleSpecs {
  engineSize: String
  engineType: String
  horsepower: Int
  torque: Int
  fuelType: FuelType!
  transmission: String
  driveType: String
  weight: Int
  length: Int
  width: Int
  height: Int
  wheelbase: Int
  fuelCapacity: Float
  oilCapacity: Float
  coolantCapacity: Float
  topSpeed: Int
  acceleration: String
  fuelEconomy: FuelEconomy
  additionalSpecs: JSON
}

enum FuelType {
  GASOLINE
  DIESEL
  ELECTRIC
  HYBRID
  E85
  OTHER
}

type FuelEconomy {
  city: Float
  highway: Float
  combined: Float
  unit: FuelEconomyUnit!
}

enum FuelEconomyUnit {
  MPG
  L_100KM
}
```

#### Maintenance Types

```graphql
type MaintenanceRecord {
  id: ID!
  vehicle: Vehicle!
  serviceType: String!
  category: ServiceCategory!
  date: Date!
  mileage: Int
  totalCost: Float!
  laborCost: Float
  partsCost: Float
  currency: String!
  description: String!
  notes: String
  serviceProvider: ServiceProvider
  warranty: ServiceWarranty
  parts: [MaintenancePart!]!
  fluids: [FluidChange!]!
  photos: [Photo!]!
  receipts: [Document!]!
  rating: Int
  feedback: String
  scheduledDate: Date
  overdueBy: Int
  nextServiceDate: Date
  nextServiceMileage: Int
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum ServiceCategory {
  OIL_CHANGE
  TIRE_SERVICE
  BRAKE_SERVICE
  FILTER_REPLACEMENT
  FLUID_SERVICE
  ANNUAL_SERVICE
  MAJOR_SERVICE
  INSPECTION
  ENGINE_REPAIR
  TRANSMISSION_REPAIR
  ELECTRICAL_REPAIR
  BODY_REPAIR
  PERFORMANCE_MOD
  COSMETIC_MOD
  ACCESSORY_INSTALL
  RECALL_SERVICE
  WARRANTY_REPAIR
  DIAGNOSTIC
  OTHER
}

type ServiceProvider {
  id: ID!
  name: String!
  type: ServiceProviderType!
  address: String
  phone: String
  website: String
  rating: Float
  reviewCount: Int
}

enum ServiceProviderType {
  DEALERSHIP
  INDEPENDENT
  CHAIN
  SELF
}

type ServiceWarranty {
  duration: Int # months
  mileage: Int
  description: String
  expiresAt: Date
}

type MaintenancePart {
  id: ID!
  name: String!
  partNumber: String
  brand: String
  category: PartCategory!
  quantity: Int!
  unitCost: Float
  totalCost: Float
  oem: Boolean!
  warranty: PartWarranty
  affiliateLink: String
  affiliatePartner: AffiliatePartner
  affiliateCommission: Float
  installationNotes: String
  installedBy: InstallationType!
}

enum PartCategory {
  OIL_FILTER
  AIR_FILTER
  FUEL_FILTER
  SPARK_PLUG
  BELT
  ENGINE_OIL
  BRAKE_FLUID
  COOLANT
  TRANSMISSION_FLUID
  BRAKE_PAD
  BRAKE_ROTOR
  BRAKE_LINE
  TIRE
  WHEEL
  TIRE_VALVE
  BATTERY
  BULB
  FUSE
  WINDSHIELD
  MIRROR
  COVER
  OTHER
}

enum InstallationType {
  SELF
  PROFESSIONAL
}

type PartWarranty {
  duration: Int # months
  description: String
  expiresAt: Date
}

type FluidChange {
  id: ID!
  fluidType: FluidType!
  quantityAdded: Float!
  quantityUnit: FluidUnit!
  brand: String
  viscosity: String
  oldFluidCondition: FluidCondition
  changeReason: String
}

enum FluidType {
  ENGINE_OIL
  BRAKE_FLUID
  COOLANT
  TRANSMISSION_FLUID
  DIFFERENTIAL_OIL
  HYDRAULIC_FLUID
  POWER_STEERING_FLUID
  WINDSHIELD_WASHER
  OTHER
}

enum FluidUnit {
  QUARTS
  LITERS
  OUNCES
}

enum FluidCondition {
  GOOD
  FAIR
  POOR
  CRITICAL
}
```

#### Scheduling Types

```graphql
type MaintenanceSchedule {
  id: ID!
  vehicle: Vehicle!
  items: [ScheduleItem!]!
  autoReminders: Boolean!
  reminderDays: [Int!]!
  source: ScheduleSource!
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum ScheduleSource {
  MANUFACTURER
  CUSTOM
  MIXED
}

type ScheduleItem {
  id: ID!
  serviceType: String!
  description: String!
  category: ServiceCategory!
  intervalMiles: Int
  intervalMonths: Int
  intervalType: IntervalType!
  nextDueMileage: Int
  nextDueDate: Date
  status: ScheduleStatus!
  priority: Priority!
  estimatedCost: Float
  estimatedDuration: Int # minutes
  recommendedParts: [RecommendedPart!]!
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum IntervalType {
  MILEAGE
  TIME
  BOTH
}

enum ScheduleStatus {
  UPCOMING
  DUE
  OVERDUE
  COMPLETED
}

enum Priority {
  LOW
  MEDIUM
  HIGH
  CRITICAL
}

type RecommendedPart {
  id: ID!
  name: String!
  category: PartCategory!
  partNumber: String
  brand: String
  quantity: Int!
  priceRange: PriceRange!
  affiliateOptions: [AffiliateOption!]!
  compatibility: CompatibilityLevel!
}

enum CompatibilityLevel {
  EXACT_FIT
  UNIVERSAL
  REQUIRES_CHECK
}

type PriceRange {
  min: Float!
  max: Float!
  currency: String!
}

type AffiliateOption {
  partner: AffiliatePartner!
  productId: String!
  productName: String!
  productUrl: String!
  price: Float!
  inStock: Boolean!
  rating: Float
  reviewCount: Int
  shippingInfo: String
}

enum AffiliatePartner {
  AMAZON
  REVZILLA
  AUTOZONE
  ADVANCE_AUTO
  EBAY_MOTORS
  CYCLE_GEAR
  DENNIS_KIRK
  OTHER
}
```

#### Media Types

```graphql
type Photo {
  id: ID!
  vehicle: Vehicle
  maintenanceRecord: MaintenanceRecord
  user: User!
  filename: String!
  originalFilename: String!
  mimeType: String!
  fileSize: Int!
  storageUrl: String!
  thumbnailUrl: String
  storageProvider: StorageProvider!
  caption: String
  tags: [String!]!
  width: Int
  height: Int
  orientation: Int
  latitude: Float
  longitude: Float
  location: String
  createdAt: DateTime!
  updatedAt: DateTime!
}

type Document {
  id: ID!
  vehicle: Vehicle
  maintenanceRecord: MaintenanceRecord
  user: User!
  title: String!
  filename: String!
  originalFilename: String!
  mimeType: String!
  fileSize: Int!
  documentType: DocumentType!
  category: String!
  storageUrl: String!
  storageProvider: StorageProvider!
  ocrText: String
  ocrConfidence: Float
  searchableContent: String
  pageCount: Int
  encrypted: Boolean!
  accessLevel: AccessLevel!
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum StorageProvider {
  S3
  CLOUDINARY
  LOCAL
}

enum DocumentType {
  REGISTRATION
  TITLE
  INSURANCE
  RECEIPT
  SERVICE_RECORD
  WARRANTY
  RECALL_NOTICE
  OWNERS_MANUAL
  SERVICE_MANUAL
  PARTS_CATALOG
  PURCHASE_AGREEMENT
  LOAN_DOCUMENT
  OTHER
}

enum AccessLevel {
  PRIVATE
  LIMITED
  PUBLIC
}
```

#### Notification Types

```graphql
type Notification {
  id: ID!
  user: User!
  title: String!
  message: String!
  type: NotificationType!
  priority: Priority!
  vehicle: Vehicle
  maintenanceRecord: MaintenanceRecord
  scheduleItem: ScheduleItem
  actionType: ActionType
  actionUrl: String
  actionText: String
  status: NotificationStatus!
  channels: [NotificationChannel!]!
  scheduledFor: DateTime
  sentAt: DateTime
  readAt: DateTime
  metadata: JSON
  createdAt: DateTime!
  updatedAt: DateTime!
}

enum NotificationType {
  MAINTENANCE_DUE
  MAINTENANCE_OVERDUE
  SERVICE_REMINDER
  WARRANTY_EXPIRING
  WARRANTY_EXPIRED
  PART_RECOMMENDATION
  PRICE_ALERT
  SALE_NOTIFICATION
  WELCOME
  ACCOUNT_UPDATE
  SECURITY_ALERT
  WINTERIZATION
  SPRING_PREP
  STORAGE_REMINDER
}

enum ActionType {
  LINK
  DEEP_LINK
  AFFILIATE
}

enum NotificationStatus {
  PENDING
  SENT
  DELIVERED
  READ
  DISMISSED
}

enum NotificationChannel {
  PUSH
  EMAIL
  SMS
  IN_APP
}
```

### Scalar Types

```graphql
scalar DateTime
scalar Date
scalar JSON
scalar Upload
```

## Queries

### User Queries

```graphql
type Query {
  # Get current user profile
  me: User!
  
  # Get user by ID (admin only)
  user(id: ID!): User
  
  # Search users (admin only)
  users(
    limit: Int = 20
    offset: Int = 0
    search: String
    accountType: AccountType
  ): UserConnection!
}

type UserConnection {
  nodes: [User!]!
  totalCount: Int!
  hasNextPage: Boolean!
}
```

### Vehicle Queries

```graphql
extend type Query {
  # Get user's vehicles
  vehicles(
    status: VehicleStatus
    vehicleType: VehicleType
    orderBy: VehicleOrderBy = CREATED_AT
    orderDirection: OrderDirection = DESC
  ): [Vehicle!]!
  
  # Get specific vehicle
  vehicle(id: ID!): Vehicle
  
  # Search vehicles
  searchVehicles(
    query: String!
    limit: Int = 20
    offset: Int = 0
  ): VehicleConnection!
  
  # Get vehicle by VIN
  vehicleByVin(vin: String!): Vehicle
  
  # Lookup vehicle specs by VIN
  vinLookup(vin: String!): VinLookupResult!
}

enum VehicleOrderBy {
  CREATED_AT
  NAME
  MAKE
  MODEL
  YEAR
  MILEAGE
  LAST_SERVICE
}

enum OrderDirection {
  ASC
  DESC
}

type VehicleConnection {
  nodes: [Vehicle!]!
  totalCount: Int!
  hasNextPage: Boolean!
}

type VinLookupResult {
  success: Boolean!
  vehicle: VehicleSpecs
  error: String
}
```

### Maintenance Queries

```graphql
extend type Query {
  # Get maintenance records for a vehicle
  maintenanceRecords(
    vehicleId: ID!
    category: ServiceCategory
    dateFrom: Date
    dateTo: Date
    limit: Int = 50
    offset: Int = 0
    orderBy: MaintenanceOrderBy = DATE
    orderDirection: OrderDirection = DESC
  ): MaintenanceRecordConnection!
  
  # Get specific maintenance record
  maintenanceRecord(id: ID!): MaintenanceRecord
  
  # Get maintenance schedule for vehicle
  maintenanceSchedule(vehicleId: ID!): MaintenanceSchedule
  
  # Get upcoming maintenance
  upcomingMaintenance(
    vehicleId: ID
    daysAhead: Int = 30
  ): [ScheduleItem!]!
  
  # Get overdue maintenance
  overdueMaintenance(vehicleId: ID): [ScheduleItem!]!
  
  # Get maintenance statistics
  maintenanceStats(
    vehicleId: ID
    dateFrom: Date
    dateTo: Date
  ): MaintenanceStats!
}

enum MaintenanceOrderBy {
  DATE
  COST
  MILEAGE
  SERVICE_TYPE
}

type MaintenanceRecordConnection {
  nodes: [MaintenanceRecord!]!
  totalCount: Int!
  hasNextPage: Boolean!
  totalCost: Float!
  averageCost: Float!
}

type MaintenanceStats {
  totalRecords: Int!
  totalCost: Float!
  averageCost: Float!
  costPerMile: Float
  mostCommonService: String
  serviceFrequency: [ServiceFrequencyItem!]!
  monthlyCosts: [MonthlyCost!]!
}

type ServiceFrequencyItem {
  serviceType: String!
  count: Int!
  totalCost: Float!
  averageCost: Float!
}

type MonthlyCost {
  month: String!
  cost: Float!
  recordCount: Int!
}
```

### Recommendation Queries

```graphql
extend type Query {
  # Get part recommendations for vehicle
  partRecommendations(
    vehicleId: ID!
    category: PartCategory
    upcoming: Boolean = true
  ): [RecommendedPart!]!
  
  # Get affiliate options for a part
  affiliateOptions(
    vehicleId: ID!
    partCategory: PartCategory!
    partName: String
  ): [AffiliateOption!]!
  
  # Search parts catalog
  searchParts(
    vehicleId: ID!
    query: String!
    category: PartCategory
    limit: Int = 20
  ): [RecommendedPart!]!
}
```

### Notification Queries

```graphql
extend type Query {
  # Get user notifications
  notifications(
    status: NotificationStatus
    type: NotificationType
    limit: Int = 50
    offset: Int = 0
  ): NotificationConnection!
  
  # Get unread notification count
  unreadNotificationCount: Int!
}

type NotificationConnection {
  nodes: [Notification!]!
  totalCount: Int!
  hasNextPage: Boolean!
  unreadCount: Int!
}
```

## Mutations

### Authentication Mutations

```graphql
type Mutation {
  # User registration
  register(input: RegisterInput!): AuthPayload!
  
  # User login
  login(input: LoginInput!): AuthPayload!
  
  # Refresh access token
  refreshToken(refreshToken: String!): AuthPayload!
  
  # Logout (invalidate refresh token)
  logout: Boolean!
  
  # Password reset request
  requestPasswordReset(email: String!): Boolean!
  
  # Reset password with token
  resetPassword(input: ResetPasswordInput!): AuthPayload!
  
  # Verify email address
  verifyEmail(token: String!): Boolean!
  
  # Resend verification email
  resendVerificationEmail: Boolean!
}

input RegisterInput {
  email: String!
  password: String!
  name: String!
  phone: String
  timezone: String
  locale: String
}

input LoginInput {
  email: String!
  password: String!
}

input ResetPasswordInput {
  token: String!
  password: String!
}

type AuthPayload {
  success: Boolean!
  user: User
  accessToken: String
  refreshToken: String
  expiresIn: Int
  error: String
}
```

### User Mutations

```graphql
extend type Mutation {
  # Update user profile
  updateUser(input: UpdateUserInput!): User!
  
  # Update user preferences
  updateUserPreferences(input: UpdateUserPreferencesInput!): UserPreferences!
  
  # Upload avatar
  uploadAvatar(file: Upload!): User!
  
  # Delete user account
  deleteAccount(password: String!): Boolean!
}

input UpdateUserInput {
  name: String
  phone: String
  timezone: String
  locale: String
}

input UpdateUserPreferencesInput {
  emailNotifications: Boolean
  pushNotifications: Boolean
  smsNotifications: Boolean
  maintenanceReminders: Boolean
  warrantyExpirations: Boolean
  partRecommendations: Boolean
  seasonalReminders: Boolean
  promotionalOffers: Boolean
  maintenanceReminderDays: [Int!]
  warrantyReminderDays: [Int!]
  distanceUnit: DistanceUnit
  currencyCode: String
  dateFormat: String
  dataSharing: Boolean
  analytics: Boolean
}
```

### Vehicle Mutations

```graphql
extend type Mutation {
  # Create vehicle
  createVehicle(input: CreateVehicleInput!): Vehicle!
  
  # Update vehicle
  updateVehicle(id: ID!, input: UpdateVehicleInput!): Vehicle!
  
  # Delete vehicle
  deleteVehicle(id: ID!): Boolean!
  
  # Update vehicle mileage
  updateVehicleMileage(id: ID!, mileage: Int!): Vehicle!
  
  # Upload vehicle photos
  uploadVehiclePhotos(vehicleId: ID!, files: [Upload!]!): [Photo!]!
  
  # Upload vehicle documents
  uploadVehicleDocuments(vehicleId: ID!, files: [Upload!]!, documentType: DocumentType!): [Document!]!
}

input CreateVehicleInput {
  name: String!
  make: String!
  model: String!
  year: Int!
  vin: String
  licensePlate: String
  vehicleType: VehicleType!
  category: VehicleCategory
  color: String
  mileage: Int!
  mileageUnit: DistanceUnit!
  purchaseDate: Date
  purchasePrice: Float
  purchaseMileage: Int
  currentValue: Float
  status: VehicleStatus = ACTIVE
  location: String
  specifications: VehicleSpecsInput
}

input UpdateVehicleInput {
  name: String
  licensePlate: String
  color: String
  mileage: Int
  currentValue: Float
  status: VehicleStatus
  location: String
  specifications: VehicleSpecsInput
}

input VehicleSpecsInput {
  engineSize: String
  engineType: String
  horsepower: Int
  torque: Int
  fuelType: FuelType
  transmission: String
  driveType: String
  weight: Int
  length: Int
  width: Int
  height: Int
  wheelbase: Int
  fuelCapacity: Float
  oilCapacity: Float
  coolantCapacity: Float
  topSpeed: Int
  acceleration: String
  fuelEconomy: FuelEconomyInput
  additionalSpecs: JSON
}

input FuelEconomyInput {
  city: Float
  highway: Float
  combined: Float
  unit: FuelEconomyUnit!
}
```

### Maintenance Mutations

```graphql
extend type Mutation {
  # Create maintenance record
  createMaintenanceRecord(input: CreateMaintenanceRecordInput!): MaintenanceRecord!
  
  # Update maintenance record
  updateMaintenanceRecord(id: ID!, input: UpdateMaintenanceRecordInput!): MaintenanceRecord!
  
  # Delete maintenance record
  deleteMaintenanceRecord(id: ID!): Boolean!
  
  # Quick oil change entry
  quickOilChange(input: QuickOilChangeInput!): MaintenanceRecord!
  
  # Update maintenance schedule
  updateMaintenanceSchedule(vehicleId: ID!, input: UpdateMaintenanceScheduleInput!): MaintenanceSchedule!
  
  # Mark schedule item as completed
  completeScheduleItem(id: ID!, maintenanceRecordId: ID!): ScheduleItem!
  
  # Snooze schedule item
  snoozeScheduleItem(id: ID!, days: Int!): ScheduleItem!
}

input CreateMaintenanceRecordInput {
  vehicleId: ID!
  serviceType: String!
  category: ServiceCategory!
  date: Date!
  mileage: Int
  totalCost: Float!
  laborCost: Float
  partsCost: Float
  currency: String!
  description: String!
  notes: String
  serviceProvider: ServiceProviderInput
  warranty: ServiceWarrantyInput
  parts: [MaintenancePartInput!]
  fluids: [FluidChangeInput!]
  rating: Int
  feedback: String
  scheduledDate: Date
}

input UpdateMaintenanceRecordInput {
  serviceType: String
  category: ServiceCategory
  date: Date
  mileage: Int
  totalCost: Float
  laborCost: Float
  partsCost: Float
  description: String
  notes: String
  serviceProvider: ServiceProviderInput
  warranty: ServiceWarrantyInput
  rating: Int
  feedback: String
}

input QuickOilChangeInput {
  vehicleId: ID!
  date: Date!
  mileage: Int!
  cost: Float!
  oilType: String
  oilQuantity: Float
  filterBrand: String
  notes: String
}

input ServiceProviderInput {
  name: String!
  type: ServiceProviderType!
  address: String
  phone: String
  website: String
}

input ServiceWarrantyInput {
  duration: Int
  mileage: Int
  description: String
}

input MaintenancePartInput {
  name: String!
  partNumber: String
  brand: String
  category: PartCategory!
  quantity: Int!
  unitCost: Float
  totalCost: Float
  oem: Boolean = false
  warranty: PartWarrantyInput
  installationNotes: String
  installedBy: InstallationType = SELF
}

input PartWarrantyInput {
  duration: Int
  description: String
}

input FluidChangeInput {
  fluidType: FluidType!
  quantityAdded: Float!
  quantityUnit: FluidUnit!
  brand: String
  viscosity: String
  oldFluidCondition: FluidCondition
  changeReason: String
}

input UpdateMaintenanceScheduleInput {
  autoReminders: Boolean
  reminderDays: [Int!]
  items: [ScheduleItemInput!]
}

input ScheduleItemInput {
  id: ID
  serviceType: String!
  description: String!
  category: ServiceCategory!
  intervalMiles: Int
  intervalMonths: Int
  intervalType: IntervalType!
  priority: Priority = MEDIUM
  estimatedCost: Float
  estimatedDuration: Int
}
```

### Media Mutations

```graphql
extend type Mutation {
  # Upload photos
  uploadPhotos(input: UploadPhotosInput!): [Photo!]!
  
  # Update photo
  updatePhoto(id: ID!, input: UpdatePhotoInput!): Photo!
  
  # Delete photo
  deletePhoto(id: ID!): Boolean!
  
  # Upload documents
  uploadDocuments(input: UploadDocumentsInput!): [Document!]!
  
  # Update document
  updateDocument(id: ID!, input: UpdateDocumentInput!): Document!
  
  # Delete document
  deleteDocument(id: ID!): Boolean!
}

input UploadPhotosInput {
  files: [Upload!]!
  vehicleId: ID
  maintenanceRecordId: ID
  captions: [String!]
  tags: [String!]
}

input UpdatePhotoInput {
  caption: String
  tags: [String!]
}

input UploadDocumentsInput {
  files: [Upload!]!
  vehicleId: ID
  maintenanceRecordId: ID
  documentType: DocumentType!
  titles: [String!]
  category: String
}

input UpdateDocumentInput {
  title: String
  category: String
  accessLevel: AccessLevel
}
```

### Notification Mutations

```graphql
extend type Mutation {
  # Mark notification as read
  markNotificationRead(id: ID!): Notification!
  
  # Mark all notifications as read
  markAllNotificationsRead: Int!
  
  # Dismiss notification
  dismissNotification(id: ID!): Boolean!
  
  # Update notification preferences
  updateNotificationPreferences(input: NotificationPreferencesInput!): UserPreferences!
}

input NotificationPreferencesInput {
  emailNotifications: Boolean
  pushNotifications: Boolean
  smsNotifications: Boolean
  maintenanceReminders: Boolean
  warrantyExpirations: Boolean
  partRecommendations: Boolean
  seasonalReminders: Boolean
  promotionalOffers: Boolean
}
```

## Subscriptions

```graphql
type Subscription {
  # Subscribe to user notifications
  notificationAdded(userId: ID!): Notification!
  
  # Subscribe to maintenance reminders
  maintenanceReminder(vehicleId: ID!): ScheduleItem!
  
  # Subscribe to part recommendations
  partRecommendation(vehicleId: ID!): RecommendedPart!
  
  # Subscribe to price alerts
  priceAlert(userId: ID!): PriceAlert!
}

type PriceAlert {
  id: ID!
  recommendedPart: RecommendedPart!
  oldPrice: Float!
  newPrice: Float!
  discount: Float!
  affiliateOption: AffiliateOption!
}
```

## Error Handling

### Error Types

```graphql
type Error {
  message: String!
  code: String!
  field: String
  details: JSON
}

enum ErrorCode {
  UNAUTHORIZED
  FORBIDDEN
  NOT_FOUND
  VALIDATION_ERROR
  DUPLICATE_ENTRY
  RATE_LIMITED
  INTERNAL_ERROR
  EXTERNAL_SERVICE_ERROR
}
```

### Common Error Responses

- **401 Unauthorized**: Invalid or expired token
- **403 Forbidden**: Insufficient permissions
- **404 Not Found**: Resource doesn't exist
- **400 Bad Request**: Validation errors
- **429 Too Many Requests**: Rate limit exceeded
- **500 Internal Server Error**: Server error

## Rate Limiting

- **Authentication**: 10 requests per minute
- **File Uploads**: 20 requests per hour
- **General API**: 1000 requests per hour
- **Search**: 100 requests per hour

## File Upload

### Supported Formats

**Photos**: JPG, PNG, HEIC (max 10MB each)
**Documents**: PDF, JPG, PNG (max 25MB each)

### Upload Process

1. Request upload URL from mutation
2. Upload file to returned URL
3. File processing (OCR, compression, etc.)
4. Database record created with file metadata

## Pagination

### Cursor-based Pagination

```graphql
type PageInfo {
  hasNextPage: Boolean!
  hasPreviousPage: Boolean!
  startCursor: String
  endCursor: String
}

interface Connection {
  pageInfo: PageInfo!
  totalCount: Int!
}
```

### Usage Example

```graphql
query GetMaintenanceRecords($vehicleId: ID!, $first: Int!, $after: String) {
  maintenanceRecords(vehicleId: $vehicleId, first: $first, after: $after) {
    nodes {
      id
      serviceType
      date
      totalCost
    }
    pageInfo {
      hasNextPage
      endCursor
    }
    totalCount
  }
}
```