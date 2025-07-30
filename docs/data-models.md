# My Garage - Data Models

## Core Entity Relationships

```
User ──────┬─────── Vehicle ──────┬─────── MaintenanceRecord
           │                      │
           │                      ├─────── MaintenanceSchedule
           │                      │
           │                      └─────── VehicleDocument
           │
           ├─────── UserPreferences
           │
           ├─────── Notification
           │
           └─────── AffiliateClick
```

## Data Models

### 1. User Management

#### User
```typescript
interface User {
  id: string                    // UUID primary key
  email: string                 // Unique, required
  emailVerified: boolean
  name: string
  phone?: string
  avatar?: string               // URL to profile image
  
  // Account Management
  accountType: 'FREE' | 'PREMIUM' | 'FLEET'
  subscriptionStatus: 'ACTIVE' | 'CANCELLED' | 'EXPIRED'
  subscriptionExpiresAt?: Date
  
  // Authentication
  passwordHash: string
  lastLoginAt?: Date
  loginCount: number
  
  // Settings
  preferences: UserPreferences
  timezone: string              // Default: UTC
  locale: string                // Default: 'en-US'
  
  // Audit
  createdAt: Date
  updatedAt: Date
  deletedAt?: Date              // Soft delete
}
```

#### UserPreferences
```typescript
interface UserPreferences {
  id: string
  userId: string                // Foreign key to User
  
  // Notification Preferences
  emailNotifications: boolean
  pushNotifications: boolean
  smsNotifications: boolean
  
  // Notification Types
  maintenanceReminders: boolean
  warrantyExpirations: boolean
  partRecommendations: boolean
  seasonalReminders: boolean
  promotionalOffers: boolean
  
  // Reminder Timing (days before due)
  maintenanceReminderDays: number[]  // e.g., [30, 7, 1]
  warrantyReminderDays: number[]     // e.g., [90, 30, 7]
  
  // Display Preferences
  distanceUnit: 'MILES' | 'KILOMETERS'
  currencyCode: string          // ISO currency code
  dateFormat: string
  
  // Privacy
  dataSharing: boolean
  analytics: boolean
  
  createdAt: Date
  updatedAt: Date
}
```

### 2. Vehicle Management

#### Vehicle
```typescript
interface Vehicle {
  id: string                    // UUID primary key
  userId: string                // Foreign key to User
  
  // Basic Information
  name: string                  // User-defined nickname
  make: string                  // e.g., "Aprilia", "Honda", "BMW"
  model: string                 // e.g., "Tuono V4", "Civic", "R1250GS"
  year: number
  vin?: string                  // Vehicle Identification Number
  licensePlate?: string
  
  // Vehicle Classification
  vehicleType: VehicleType
  category: VehicleCategory     // Derived from type and specs
  
  // Physical Attributes
  color?: string
  mileage: number               // Current odometer reading
  mileageUnit: 'MILES' | 'KILOMETERS'
  
  // Ownership Information
  purchaseDate?: Date
  purchasePrice?: number
  purchaseMileage?: number
  currentValue?: number         // Estimated current value
  
  // Status
  status: 'ACTIVE' | 'SOLD' | 'TOTALED' | 'STORED'
  location?: string             // Where vehicle is kept
  
  // Specifications (JSON field)
  specifications: VehicleSpecs
  
  // Maintenance
  lastServiceDate?: Date
  lastServiceMileage?: number
  nextServiceDue?: Date
  nextServiceMileage?: number
  healthScore?: number          // 0-100, calculated field
  
  // Media
  primaryPhotoUrl?: string
  photos: Photo[]
  documents: VehicleDocument[]
  
  // Schedule
  maintenanceSchedule: MaintenanceSchedule
  
  // Audit
  createdAt: Date
  updatedAt: Date
  deletedAt?: Date
}
```

#### VehicleType
```typescript
enum VehicleType {
  MOTORCYCLE = 'MOTORCYCLE',
  CAR = 'CAR',
  TRUCK = 'TRUCK',
  SUV = 'SUV',
  BOAT = 'BOAT',
  BICYCLE = 'BICYCLE',
  ATV = 'ATV',
  JETSKI = 'JETSKI',
  RV = 'RV',
  TRAILER = 'TRAILER',
  EQUIPMENT = 'EQUIPMENT',      // Lawn mowers, generators, etc.
  OTHER = 'OTHER'
}
```

#### VehicleCategory
```typescript
enum VehicleCategory {
  // Motorcycles
  SPORT_BIKE = 'SPORT_BIKE',
  CRUISER = 'CRUISER',
  TOURING = 'TOURING',
  ADVENTURE = 'ADVENTURE',
  DIRT_BIKE = 'DIRT_BIKE',
  SCOOTER = 'SCOOTER',
  
  // Cars
  SEDAN = 'SEDAN',
  HATCHBACK = 'HATCHBACK',
  COUPE = 'COUPE',
  CONVERTIBLE = 'CONVERTIBLE',
  WAGON = 'WAGON',
  
  // Trucks/SUVs
  PICKUP = 'PICKUP',
  COMPACT_SUV = 'COMPACT_SUV',
  MIDSIZE_SUV = 'MIDSIZE_SUV',
  FULL_SIZE_SUV = 'FULL_SIZE_SUV',
  
  // Boats
  PONTOON = 'PONTOON',
  BASS_BOAT = 'BASS_BOAT',
  SAILBOAT = 'SAILBOAT',
  YACHT = 'YACHT',
  
  // Other
  ROAD_BIKE = 'ROAD_BIKE',
  MOUNTAIN_BIKE = 'MOUNTAIN_BIKE',
  OTHER = 'OTHER'
}
```

#### VehicleSpecs
```typescript
interface VehicleSpecs {
  // Engine
  engineSize?: string           // e.g., "1077cc", "2.0L", "125cc"
  engineType?: string           // e.g., "V4", "Inline-4", "Single"
  horsepower?: number
  torque?: number
  fuelType: 'GASOLINE' | 'DIESEL' | 'ELECTRIC' | 'HYBRID' | 'E85' | 'OTHER'
  
  // Transmission
  transmission?: string         // e.g., "6-speed manual", "CVT", "Automatic"
  driveType?: string           // e.g., "FWD", "RWD", "AWD", "Chain"
  
  // Physical
  weight?: number              // lbs or kg
  length?: number              // inches or cm
  width?: number
  height?: number
  wheelbase?: number
  
  // Capacity
  fuelCapacity?: number        // gallons or liters
  oilCapacity?: number         // quarts or liters
  coolantCapacity?: number
  
  // Performance
  topSpeed?: number
  acceleration?: string        // e.g., "0-60 in 3.2s"
  fuelEconomy?: FuelEconomy
  
  // Additional specs by vehicle type
  additionalSpecs: Record<string, any>
}
```

#### FuelEconomy
```typescript
interface FuelEconomy {
  city?: number                // MPG or L/100km
  highway?: number
  combined?: number
  unit: 'MPG' | 'L_100KM'
}
```

### 3. Maintenance Management

#### MaintenanceRecord
```typescript
interface MaintenanceRecord {
  id: string                   // UUID primary key
  vehicleId: string            // Foreign key to Vehicle
  
  // Service Information
  serviceType: string          // e.g., "Oil Change", "Tire Rotation", "Annual Service"
  category: ServiceCategory
  date: Date                   // When service was performed
  mileage?: number            // Odometer reading at time of service
  
  // Cost Information
  totalCost: number
  laborCost?: number
  partsCost?: number
  currency: string             // ISO currency code
  
  // Service Details
  description: string          // Detailed description of work performed
  notes?: string              // Additional notes
  
  // Service Provider
  serviceProvider?: ServiceProvider
  warranty?: ServiceWarranty
  
  // Parts and Materials
  parts: MaintenancePart[]
  fluids: FluidChange[]
  
  // Documentation
  photos: Photo[]
  receipts: Document[]
  
  // Quality Assurance
  rating?: number             // 1-5 rating of service
  feedback?: string
  
  // Scheduling
  scheduledDate?: Date        // If it was a scheduled service
  overdueBy?: number          // Days overdue when performed
  
  // Next Service
  nextServiceDate?: Date      // When next similar service is due
  nextServiceMileage?: number
  
  // Audit
  createdAt: Date
  updatedAt: Date
  deletedAt?: Date
}
```

#### ServiceCategory
```typescript
enum ServiceCategory {
  // Regular Maintenance
  OIL_CHANGE = 'OIL_CHANGE',
  TIRE_SERVICE = 'TIRE_SERVICE',
  BRAKE_SERVICE = 'BRAKE_SERVICE',
  FILTER_REPLACEMENT = 'FILTER_REPLACEMENT',
  FLUID_SERVICE = 'FLUID_SERVICE',
  
  // Scheduled Maintenance
  ANNUAL_SERVICE = 'ANNUAL_SERVICE',
  MAJOR_SERVICE = 'MAJOR_SERVICE',
  INSPECTION = 'INSPECTION',
  
  // Repairs
  ENGINE_REPAIR = 'ENGINE_REPAIR',
  TRANSMISSION_REPAIR = 'TRANSMISSION_REPAIR',
  ELECTRICAL_REPAIR = 'ELECTRICAL_REPAIR',
  BODY_REPAIR = 'BODY_REPAIR',
  
  // Upgrades
  PERFORMANCE_MOD = 'PERFORMANCE_MOD',
  COSMETIC_MOD = 'COSMETIC_MOD',
  ACCESSORY_INSTALL = 'ACCESSORY_INSTALL',
  
  // Other
  RECALL_SERVICE = 'RECALL_SERVICE',
  WARRANTY_REPAIR = 'WARRANTY_REPAIR',
  DIAGNOSTIC = 'DIAGNOSTIC',
  OTHER = 'OTHER'
}
```

#### MaintenancePart
```typescript
interface MaintenancePart {
  id: string
  maintenanceRecordId: string  // Foreign key
  
  // Part Information
  name: string                 // e.g., "Oil Filter", "Spark Plug"
  partNumber?: string          // Manufacturer part number
  brand?: string               // e.g., "OEM", "K&N", "NGK"
  category: PartCategory
  
  // Quantity and Cost
  quantity: number
  unitCost?: number
  totalCost?: number
  
  // Part Details
  oem: boolean                 // OEM vs aftermarket
  warranty?: PartWarranty
  
  // Affiliate Tracking
  affiliateLink?: string       // If purchased through app
  affiliatePartner?: string    // Amazon, RevZilla, etc.
  affiliateCommission?: number
  
  // Installation
  installationNotes?: string
  installedBy: 'SELF' | 'PROFESSIONAL'
  
  createdAt: Date
  updatedAt: Date
}
```

#### PartCategory
```typescript
enum PartCategory {
  // Engine
  OIL_FILTER = 'OIL_FILTER',
  AIR_FILTER = 'AIR_FILTER',
  FUEL_FILTER = 'FUEL_FILTER',
  SPARK_PLUG = 'SPARK_PLUG',
  BELT = 'BELT',
  
  // Fluids
  ENGINE_OIL = 'ENGINE_OIL',
  BRAKE_FLUID = 'BRAKE_FLUID',
  COOLANT = 'COOLANT',
  TRANSMISSION_FLUID = 'TRANSMISSION_FLUID',
  
  // Brakes
  BRAKE_PAD = 'BRAKE_PAD',
  BRAKE_ROTOR = 'BRAKE_ROTOR',
  BRAKE_LINE = 'BRAKE_LINE',
  
  // Tires and Wheels
  TIRE = 'TIRE',
  WHEEL = 'WHEEL',
  TIRE_VALVE = 'TIRE_VALVE',
  
  // Electrical
  BATTERY = 'BATTERY',
  BULB = 'BULB',
  FUSE = 'FUSE',
  
  // Body and Accessories
  WINDSHIELD = 'WINDSHIELD',
  MIRROR = 'MIRROR',
  COVER = 'COVER',
  
  OTHER = 'OTHER'
}
```

#### FluidChange
```typescript
interface FluidChange {
  id: string
  maintenanceRecordId: string
  
  fluidType: FluidType
  quantityAdded: number        // Amount added
  quantityUnit: 'QUARTS' | 'LITERS' | 'OUNCES'
  brand?: string
  viscosity?: string           // e.g., "10W-40", "5W-30"
  
  // Condition Assessment
  oldFluidCondition?: 'GOOD' | 'FAIR' | 'POOR' | 'CRITICAL'
  changeReason?: string        // Regular maintenance, contaminated, etc.
  
  createdAt: Date
  updatedAt: Date
}
```

#### FluidType
```typescript
enum FluidType {
  ENGINE_OIL = 'ENGINE_OIL',
  BRAKE_FLUID = 'BRAKE_FLUID',
  COOLANT = 'COOLANT',
  TRANSMISSION_FLUID = 'TRANSMISSION_FLUID',
  DIFFERENTIAL_OIL = 'DIFFERENTIAL_OIL',
  HYDRAULIC_FLUID = 'HYDRAULIC_FLUID',
  POWER_STEERING_FLUID = 'POWER_STEERING_FLUID',
  WINDSHIELD_WASHER = 'WINDSHIELD_WASHER',
  OTHER = 'OTHER'
}
```

### 4. Scheduling and Reminders

#### MaintenanceSchedule
```typescript
interface MaintenanceSchedule {
  id: string
  vehicleId: string            // Foreign key to Vehicle
  
  // Schedule Items
  items: ScheduleItem[]
  
  // Settings
  autoReminders: boolean
  reminderDays: number[]       // Days before due to remind
  
  // Manufacturer vs Custom
  source: 'MANUFACTURER' | 'CUSTOM' | 'MIXED'
  
  createdAt: Date
  updatedAt: Date
}
```

#### ScheduleItem
```typescript
interface ScheduleItem {
  id: string
  scheduleId: string
  
  // Service Definition
  serviceType: string
  description: string
  category: ServiceCategory
  
  // Intervals
  intervalMiles?: number       // Miles between services
  intervalMonths?: number      // Months between services
  intervalType: 'MILEAGE' | 'TIME' | 'BOTH'
  
  // Next Due
  nextDueMileage?: number
  nextDueDate?: Date
  
  // Status
  status: 'UPCOMING' | 'DUE' | 'OVERDUE' | 'COMPLETED'
  priority: 'LOW' | 'MEDIUM' | 'HIGH' | 'CRITICAL'
  
  // Cost Estimation
  estimatedCost?: number
  estimatedDuration?: number   // Minutes
  
  // Parts Needed
  recommendedParts: RecommendedPart[]
  
  createdAt: Date
  updatedAt: Date
}
```

#### RecommendedPart
```typescript
interface RecommendedPart {
  id: string
  scheduleItemId: string
  
  // Part Information
  name: string
  category: PartCategory
  partNumber?: string
  brand?: string
  
  // Quantity
  quantity: number
  
  // Pricing (from affiliate partners)
  priceRange: PriceRange
  affiliateOptions: AffiliateOption[]
  
  // Compatibility
  compatibility: 'EXACT_FIT' | 'UNIVERSAL' | 'REQUIRES_CHECK'
  
  createdAt: Date
  updatedAt: Date
}
```

### 5. Media and Documents

#### Photo
```typescript
interface Photo {
  id: string
  
  // Relationships
  vehicleId?: string           // If attached to vehicle
  maintenanceRecordId?: string // If attached to maintenance record
  userId: string               // Owner
  
  // File Information
  filename: string
  originalFilename: string
  mimeType: string
  fileSize: number             // Bytes
  
  // Storage
  storageUrl: string           // Full URL to image
  thumbnailUrl?: string        // Smaller version
  storageProvider: 'S3' | 'CLOUDINARY' | 'LOCAL'
  
  // Metadata
  caption?: string
  tags: string[]               // Searchable tags
  
  // Image Properties
  width?: number
  height?: number
  orientation?: number         // EXIF orientation
  
  // Location (if enabled)
  latitude?: number
  longitude?: number
  location?: string            // Human-readable location
  
  // Audit
  createdAt: Date
  updatedAt: Date
  deletedAt?: Date
}
```

#### Document
```typescript
interface Document {
  id: string
  
  // Relationships
  vehicleId?: string
  maintenanceRecordId?: string
  userId: string
  
  // Document Information
  title: string
  filename: string
  originalFilename: string
  mimeType: string
  fileSize: number
  
  // Classification
  documentType: DocumentType
  category: string             // User-defined category
  
  // Storage
  storageUrl: string
  storageProvider: 'S3' | 'CLOUDINARY' | 'LOCAL'
  
  // OCR and Content
  ocrText?: string            // Extracted text content
  ocrConfidence?: number      // OCR confidence score
  searchableContent?: string   // Processed for search
  
  // Document Properties
  pageCount?: number
  
  // Security
  encrypted: boolean
  accessLevel: 'PRIVATE' | 'LIMITED' | 'PUBLIC'
  
  // Audit
  createdAt: Date
  updatedAt: Date
  deletedAt?: Date
}
```

#### DocumentType
```typescript
enum DocumentType {
  // Registration and Legal
  REGISTRATION = 'REGISTRATION',
  TITLE = 'TITLE',
  INSURANCE = 'INSURANCE',
  
  // Maintenance
  RECEIPT = 'RECEIPT',
  SERVICE_RECORD = 'SERVICE_RECORD',
  WARRANTY = 'WARRANTY',
  RECALL_NOTICE = 'RECALL_NOTICE',
  
  // Manuals and Guides
  OWNERS_MANUAL = 'OWNERS_MANUAL',
  SERVICE_MANUAL = 'SERVICE_MANUAL',
  PARTS_CATALOG = 'PARTS_CATALOG',
  
  // Financial
  PURCHASE_AGREEMENT = 'PURCHASE_AGREEMENT',
  LOAN_DOCUMENT = 'LOAN_DOCUMENT',
  
  OTHER = 'OTHER'
}
```

### 6. Notifications and Communication

#### Notification
```typescript
interface Notification {
  id: string
  userId: string              // Foreign key to User
  
  // Content
  title: string
  message: string
  type: NotificationType
  priority: 'LOW' | 'MEDIUM' | 'HIGH' | 'URGENT'
  
  // Context
  vehicleId?: string          // Related vehicle
  maintenanceRecordId?: string
  scheduleItemId?: string
  
  // Action
  actionType?: 'LINK' | 'DEEP_LINK' | 'AFFILIATE'
  actionUrl?: string
  actionText?: string         // Button text
  
  // Status
  status: 'PENDING' | 'SENT' | 'DELIVERED' | 'READ' | 'DISMISSED'
  
  // Delivery
  channels: NotificationChannel[]
  scheduledFor?: Date         // When to send
  sentAt?: Date
  readAt?: Date
  
  // Metadata
  metadata: Record<string, any>
  
  createdAt: Date
  updatedAt: Date
}
```

#### NotificationType
```typescript
enum NotificationType {
  // Maintenance
  MAINTENANCE_DUE = 'MAINTENANCE_DUE',
  MAINTENANCE_OVERDUE = 'MAINTENANCE_OVERDUE',
  SERVICE_REMINDER = 'SERVICE_REMINDER',
  
  // Warranty
  WARRANTY_EXPIRING = 'WARRANTY_EXPIRING',
  WARRANTY_EXPIRED = 'WARRANTY_EXPIRED',
  
  // Recommendations
  PART_RECOMMENDATION = 'PART_RECOMMENDATION',
  PRICE_ALERT = 'PRICE_ALERT',
  SALE_NOTIFICATION = 'SALE_NOTIFICATION',
  
  // System
  WELCOME = 'WELCOME',
  ACCOUNT_UPDATE = 'ACCOUNT_UPDATE',
  SECURITY_ALERT = 'SECURITY_ALERT',
  
  // Seasonal
  WINTERIZATION = 'WINTERIZATION',
  SPRING_PREP = 'SPRING_PREP',
  STORAGE_REMINDER = 'STORAGE_REMINDER'
}
```

#### NotificationChannel
```typescript
enum NotificationChannel {
  PUSH = 'PUSH',              // Mobile push notification
  EMAIL = 'EMAIL',            // Email notification
  SMS = 'SMS',                // Text message
  IN_APP = 'IN_APP'           // In-app notification only
}
```

### 7. Affiliate Marketing

#### AffiliateClick
```typescript
interface AffiliateClick {
  id: string
  userId: string
  
  // Context
  vehicleId?: string
  recommendedPartId?: string
  notificationId?: string
  
  // Affiliate Information
  partner: AffiliatePartner
  productId: string           // Partner's product ID
  productName: string
  productUrl: string          // Original affiliate URL
  
  // Click Information
  clickedAt: Date
  ipAddress: string
  userAgent: string
  
  // Conversion Tracking
  converted: boolean
  conversionValue?: number    // Purchase amount
  commission?: number         // Our commission
  conversionDate?: Date
  
  // Campaign Tracking
  campaign?: string
  source?: string
  medium?: string
  
  createdAt: Date
  updatedAt: Date
}
```

#### AffiliatePartner
```typescript
enum AffiliatePartner {
  AMAZON = 'AMAZON',
  REVZILLA = 'REVZILLA',
  AUTOZONE = 'AUTOZONE',
  ADVANCE_AUTO = 'ADVANCE_AUTO',
  EBAY_MOTORS = 'EBAY_MOTORS',
  CYCLE_GEAR = 'CYCLE_GEAR',
  DENNIS_KIRK = 'DENNIS_KIRK',
  OTHER = 'OTHER'
}
```

## Database Indexes

### Primary Indexes
- All `id` fields (UUID primary keys)
- All foreign key relationships
- `User.email` (unique)
- `Vehicle.vin` (unique, sparse)

### Performance Indexes
- `Vehicle.userId, Vehicle.status`
- `MaintenanceRecord.vehicleId, MaintenanceRecord.date DESC`
- `Notification.userId, Notification.status, Notification.scheduledFor`
- `AffiliateClick.userId, AffiliateClick.clickedAt DESC`

### Search Indexes
- `Vehicle.make, Vehicle.model, Vehicle.year`
- `MaintenanceRecord.serviceType`
- `Document.ocrText` (full-text search)
- `Photo.tags` (array index)

## Data Retention Policies

### User Data
- **Active Users**: Retain all data
- **Inactive Users** (2+ years): Archive old maintenance records
- **Deleted Accounts**: 30-day soft delete, then permanent deletion

### Media Files
- **Photos**: Compress after 1 year, archive after 3 years
- **Documents**: Keep indefinitely (legal/warranty reasons)
- **Receipts**: OCR extract, then compress originals

### Analytics Data
- **Click Tracking**: Aggregate after 1 year, keep raw data for 2 years
- **Usage Analytics**: Keep aggregated data indefinitely
- **Performance Logs**: Retain for 90 days