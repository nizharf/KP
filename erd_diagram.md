# Entity Relationship Diagram (ERD) - Asset Management System

```mermaid
erDiagram
    users {
        bigint id PK
        string name
        string email UK
        string photo_path
        string email_verified_at
        string password
        enum role{admin,user}
        boolean is_active
        timestamp last_login_at
        string remember_token
        timestamp created_at
        timestamp updated_at
    }

    assets {
        bigint id PK
        string asset_code UK
        string name
        string category
        string brand
        string model
        string serial_number
        string location
        enum status{available,in_use,maintenance,lost,retired}
        enum condition{good,fair,bad}
        date purchase_date
        decimal purchase_price(15,2)
        text notes
        text qr_payload
        string qr_path
        bigint created_by FK
        bigint updated_by FK
        timestamp created_at
        timestamp updated_at
    }

    asset_transfers {
        bigint id PK
        bigint asset_id FK
        string from_location
        string to_location
        timestamp transfer_date
        bigint moved_by FK
        bigint approved_by FK
        text note
        timestamp created_at
        timestamp updated_at
    }

    users ||--o{ assets : "creates/updates"
    users ||--o{ asset_transfers : "moves (moved_by)"
    users ||--o{ asset_transfers : "approves (approved_by)"
    assets ||--o{ asset_transfers : "has transfers"
```

## Database Schema Details

### Users Table
**Primary Key:** `id` (bigint, auto-increment)

**Columns:**
- `id` - Primary key (bigint)
- `name` - User full name (string)
- `email` - Unique email address (string, unique)
- `photo_path` - Profile photo file path (string, nullable)
- `email_verified_at` - Email verification timestamp (timestamp, nullable)
- `password` - Hashed password (string)
- `role` - User role: 'admin' or 'user' (enum, default: 'user')
- `is_active` - Account status (boolean, default: true)
- `last_login_at` - Last login timestamp (timestamp, nullable)
- `remember_token` - Remember me token (string, nullable)
- `created_at` - Record creation timestamp
- `updated_at` - Record update timestamp

**Indexes:**
- Primary key on `id`
- Unique index on `email`

### Assets Table
**Primary Key:** `id` (bigint, auto-increment)

**Columns:**
- `id` - Primary key (bigint)
- `asset_code` - Unique asset identifier (string, unique)
- `name` - Asset description/name (string)
- `category` - Asset category (string, nullable)
- `brand` - Asset brand (string, nullable)
- `model` - Asset model (string, nullable)
- `serial_number` - Serial number (string, nullable)
- `location` - Current location (string, nullable)
- `status` - Asset status: 'available', 'in_use', 'maintenance', 'lost', 'retired' (enum, default: 'available')
- `condition` - Asset condition: 'good', 'fair', 'bad' (enum, default: 'good')
- `purchase_date` - Purchase date (date, nullable)
- `purchase_price` - Purchase price (decimal 15,2, nullable)
- `notes` - Additional notes (text, nullable)
- `qr_payload` - QR code data payload (text, nullable)
- `qr_path` - QR code image file path (string, nullable)
- `created_by` - Creator user ID (bigint, foreign key to users, nullable)
- `updated_by` - Last updater user ID (bigint, foreign key to users, nullable)
- `created_at` - Record creation timestamp
- `updated_at` - Record update timestamp

**Indexes:**
- Primary key on `id`
- Unique index on `asset_code`
- Composite index on `['asset_code', 'name', 'serial_number', 'location']`
- Foreign key on `created_by` → users.id (null on delete)
- Foreign key on `updated_by` → users.id (null on delete)

### Asset Transfers Table
**Primary Key:** `id` (bigint, auto-increment)

**Columns:**
- `id` - Primary key (bigint)
- `asset_id` - Related asset ID (bigint, foreign key to assets)
- `from_location` - Source location (string, nullable)
- `to_location` - Destination location (string, nullable)
- `transfer_date` - Transfer timestamp (timestamp, default: current)
- `moved_by` - User who performed transfer (bigint, foreign key to users, nullable)
- `approved_by` - User who approved transfer (bigint, foreign key to users, nullable)
- `note` - Transfer notes (text, nullable)
- `created_at` - Record creation timestamp
- `updated_at` - Record update timestamp

**Indexes:**
- Primary key on `id`
- Composite index on `['asset_id', 'transfer_date']`
- Foreign key on `asset_id` → assets.id (cascade on delete)
- Foreign key on `moved_by` → users.id (null on delete)
- Foreign key on `approved_by` → users.id (null on delete)

## Relationships Summary

### One-to-Many Relationships

1. **Users → Assets** (One-to-Many)
   - One user can create/update many assets
   - Foreign keys: `assets.created_by`, `assets.updated_by`
   - Constraint: nullable, null on delete

2. **Users → Asset Transfers** (Two separate One-to-Many)
   - **As Mover**: One user can move many assets
   - **As Approver**: One user can approve many transfers
   - Foreign keys: `asset_transfers.moved_by`, `asset_transfers.approved_by`
   - Constraint: nullable, null on delete

3. **Assets → Asset Transfers** (One-to-Many)
   - One asset can have many transfer records
   - Foreign key: `asset_transfers.asset_id`
   - Constraint: cascade on delete (transfers deleted when asset deleted)

## Data Integrity Features

**Foreign Key Constraints:**
- All foreign keys maintain referential integrity
- Cascade delete for asset_transfers.asset_id
- Null on delete for user references (preserves transfer history)

**Unique Constraints:**
- users.email (unique login)
- assets.asset_code (unique asset identification)

**Enum Constraints:**
- users.role: 'admin', 'user'
- assets.status: 'available', 'in_use', 'maintenance', 'lost', 'retired'
- assets.condition: 'good', 'fair', 'bad'

**Indexes for Performance:**
- Composite index on assets for fast search
- Composite index on asset_transfers for transfer history queries
