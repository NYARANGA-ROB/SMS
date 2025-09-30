# Optional Modules Documentation

## Overview
Three powerful optional modules to extend your school management system: **HR & Payroll**, **Library Management**, and **Transport Management**. Each module is fully integrated with the core system and ready for UI implementation.

---

## 1. HR & Payroll Module

### Features
- **Staff Profiles** - Complete employee records with salary details
- **Leave Management** - Apply, approve, and track staff leave
- **Payroll Processing** - Monthly payroll with automatic calculations
- **Payslips** - Digital payslips with allowances and deductions

### Database Tables

#### `staff_profiles`
- Employee ID, department, designation
- Joining date, employment type
- Basic salary, allowances (JSON)
- Bank details, tax ID

#### `leaves`
- Leave types: Sick, Casual, Annual, Maternity, Unpaid
- Start/end dates, total days
- Status: Pending, Approved, Rejected
- Approval workflow with timestamps

#### `payrolls`
- Monthly payroll batches
- Status: Draft, Processed, Paid
- Total amount tracking

#### `payslips`
- Individual staff payslips
- Basic salary + allowances = Gross salary
- Deductions (tax, pension) = Net salary
- Working days vs present days

### Models
- `App\Models\StaffProfile`
- `App\Models\Leave`
- `App\Models\Payroll`
- `App\Models\Payslip`

### Key Features

**Staff Profile Management:**
```php
$staff = StaffProfile::create([
    'user_id' => $teacher->id,
    'employee_id' => 'EMP001',
    'designation' => 'Senior Teacher',
    'basic_salary' => 50000,
    'allowances' => ['housing' => 10000, 'transport' => 5000],
]);

$grossSalary = $staff->gross_salary; // Auto-calculated
```

**Leave Application:**
```php
$leave = Leave::create([
    'user_id' => $staff->user_id,
    'type' => 'sick',
    'start_date' => '2025-10-01',
    'end_date' => '2025-10-03',
    'days' => 3,
    'reason' => 'Medical treatment',
    'status' => 'pending',
]);
```

**Payroll Processing:**
```php
$payroll = Payroll::create([
    'month' => '2025-10',
    'status' => 'draft',
]);

// Generate payslips for all staff
foreach ($staffProfiles as $staff) {
    $payslip = Payslip::create([
        'payroll_id' => $payroll->id,
        'user_id' => $staff->user_id,
        'basic_salary' => $staff->basic_salary,
        'allowances' => $staff->allowances,
        'gross_salary' => $staff->gross_salary,
        'deductions' => ['tax' => 5000, 'pension' => 2000],
        'net_salary' => $staff->gross_salary - 7000,
    ]);
}
```

### Use Cases
- Track employee records and salary history
- Manage leave requests with approval workflow
- Generate monthly payroll automatically
- Download payslips as PDF
- Calculate tax deductions and allowances

---

## 2. Library Management Module

### Features
- **Book Catalogue** - Complete library inventory
- **Borrow/Return System** - Track book circulation
- **Fine Management** - Automatic overdue fines
- **E-Library** - Digital resources (ebooks, videos, documents)

### Database Tables

#### `books`
- ISBN, title, author, publisher
- Category, publication year
- Total copies, available copies
- Shelf location, cover image

#### `borrow_records`
- Borrow date, due date, return date
- Status: Borrowed, Returned, Overdue
- Fine amount and payment status
- Notes

#### `e_library_resources`
- Digital content: Ebooks, Videos, Audio, Documents, Links
- File storage or external URLs
- Download and view counters
- Categories and thumbnails

### Models
- `App\Models\Book`
- `App\Models\BorrowRecord`
- `App\Models\ELibraryResource`

### Key Features

**Book Management:**
```php
$book = Book::create([
    'isbn' => '978-3-16-148410-0',
    'title' => 'Introduction to Physics',
    'author' => 'John Doe',
    'category' => 'Science',
    'total_copies' => 5,
    'available_copies' => 5,
    'shelf_location' => 'A-12',
]);

if ($book->isAvailable()) {
    // Allow borrowing
}
```

**Borrow/Return:**
```php
// Borrow a book
$borrow = BorrowRecord::create([
    'book_id' => $book->id,
    'user_id' => $student->id,
    'borrow_date' => now(),
    'due_date' => now()->addDays(14),
    'status' => 'borrowed',
]);

$book->decrement('available_copies');

// Return a book
$borrow->update([
    'return_date' => now(),
    'status' => 'returned',
]);

$book->increment('available_copies');

// Check overdue
if ($borrow->isOverdue()) {
    $daysLate = now()->diffInDays($borrow->due_date);
    $fine = $daysLate * 10; // $10 per day
    $borrow->update(['fine_amount' => $fine]);
}
```

**E-Library:**
```php
$resource = ELibraryResource::create([
    'title' => 'Advanced Mathematics Video Series',
    'type' => 'video',
    'category' => 'Mathematics',
    'external_url' => 'https://youtube.com/...',
]);

$resource->incrementViews();
$resource->incrementDownloads();
```

### Use Cases
- Manage physical book inventory
- Track who borrowed which books
- Calculate and collect overdue fines
- Provide digital learning resources
- Generate library usage reports

---

## 3. Transport Management Module

### Features
- **Bus Fleet Management** - Track buses and drivers
- **Route Planning** - Define routes with stops and schedules
- **Student Assignments** - Assign students to routes
- **GPS Tracking** - Real-time bus location tracking
- **Parent Alerts** - Automatic pickup/dropoff notifications

### Database Tables

#### `buses`
- Bus number, registration number
- Driver details (name, phone, license)
- Capacity, GPS device ID
- Status: Active, Maintenance, Inactive

#### `bus_routes`
- Route name, description
- Stops (JSON with name, time, coordinates)
- Fare, departure/arrival times

#### `route_assignments`
- Student assigned to route and bus
- Pickup and drop stops
- Status: Active, Suspended

#### `bus_tracking_logs`
- Real-time GPS coordinates
- Speed, timestamp
- Historical tracking data

#### `transport_alerts`
- Alert types: Pickup, Dropoff, Delay, Emergency
- Message content
- Sent status and timestamp

### Models
- `App\Models\Bus`
- `App\Models\BusRoute`
- `App\Models\RouteAssignment`
- `App\Models\BusTrackingLog`
- `App\Models\TransportAlert`

### Key Features

**Bus Management:**
```php
$bus = Bus::create([
    'bus_number' => 'BUS-001',
    'registration_number' => 'ABC-1234',
    'driver_name' => 'John Driver',
    'driver_phone' => '+1234567890',
    'capacity' => 40,
    'gps_device_id' => 'GPS-001',
    'status' => 'active',
]);
```

**Route Planning:**
```php
$route = BusRoute::create([
    'route_name' => 'North Route',
    'stops' => [
        ['name' => 'Main Gate', 'time' => '07:00', 'lat' => 0.0, 'lng' => 0.0],
        ['name' => 'Market Square', 'time' => '07:15', 'lat' => 0.1, 'lng' => 0.1],
        ['name' => 'School', 'time' => '07:45', 'lat' => 0.2, 'lng' => 0.2],
    ],
    'fare' => 500,
    'departure_time' => '07:00',
    'arrival_time' => '07:45',
]);
```

**Student Assignment:**
```php
$assignment = RouteAssignment::create([
    'bus_route_id' => $route->id,
    'bus_id' => $bus->id,
    'user_id' => $student->id,
    'pickup_stop' => 'Main Gate',
    'drop_stop' => 'School',
    'status' => 'active',
]);
```

**GPS Tracking:**
```php
// Log GPS position (called by GPS device)
BusTrackingLog::create([
    'bus_id' => $bus->id,
    'latitude' => 0.123456,
    'longitude' => 0.654321,
    'speed' => 45, // km/h
    'tracked_at' => now(),
]);

// Get latest position
$latestPosition = $bus->trackingLogs()->latest('tracked_at')->first();
```

**Parent Alerts:**
```php
// Send pickup alert
TransportAlert::create([
    'bus_id' => $bus->id,
    'user_id' => $parent->id,
    'type' => 'pickup',
    'message' => 'Your child has been picked up at Main Gate at 07:05 AM',
    'sent' => true,
    'sent_at' => now(),
]);

// Send delay alert
TransportAlert::create([
    'bus_id' => $bus->id,
    'user_id' => $parent->id,
    'type' => 'delay',
    'message' => 'Bus is running 15 minutes late due to traffic',
]);
```

### Use Cases
- Manage school bus fleet and drivers
- Plan and optimize routes
- Assign students to buses
- Track buses in real-time on map
- Send automatic alerts to parents
- Monitor driver performance
- Calculate transport fees

---

## Integration Guide

### Adding UI Components

All models are ready with relationships. To add UI:

1. **Create Controllers** (e.g., `StaffProfileController`, `BookController`, `BusController`)
2. **Create Livewire Components** for CRUD operations
3. **Create Blade Views** with forms and datatables
4. **Add Routes** in `routes/web.php`
5. **Add Menu Items** in `app/Livewire/Layouts/Menu.php`

### Example: Adding Staff Profile UI

```php
// Controller
class StaffProfileController extends Controller {
    public function index() {
        return view('pages.hr.staff.index');
    }
}

// Livewire Component
class ListStaffProfiles extends Component {
    public function render() {
        return view('livewire.list-staff-profiles');
    }
}

// Route
Route::get('hr/staff', [StaffProfileController::class, 'index'])->name('hr.staff.index');

// Menu
[
    'type' => 'menu-item',
    'text' => 'HR & Payroll',
    'icon' => 'fas fa-users',
    'submenu' => [
        ['text' => 'Staff Profiles', 'route' => 'hr.staff.index'],
        ['text' => 'Leave Management', 'route' => 'hr.leaves.index'],
        ['text' => 'Payroll', 'route' => 'hr.payroll.index'],
    ],
]
```

### GPS Integration

For real-time tracking, integrate with GPS providers:

**Option 1: Custom GPS Devices**
- Devices send HTTP POST requests to your endpoint
- Store in `bus_tracking_logs` table

**Option 2: Third-party APIs**
- Traccar (open-source)
- GPS Gate
- Geotab

### Parent Alert Integration

Use existing announcement system or integrate:
- SMS via Twilio/Africa's Talking
- Push notifications via Firebase
- Email via Laravel Mail

---

## Database Schema Summary

### HR Module (4 tables)
- `staff_profiles` - Employee records
- `leaves` - Leave applications
- `payrolls` - Monthly payroll batches
- `payslips` - Individual payslips

### Library Module (3 tables)
- `books` - Physical book catalogue
- `borrow_records` - Circulation tracking
- `e_library_resources` - Digital content

### Transport Module (5 tables)
- `buses` - Fleet management
- `bus_routes` - Route planning
- `route_assignments` - Student assignments
- `bus_tracking_logs` - GPS tracking
- `transport_alerts` - Parent notifications

**Total: 12 new tables, all migrated and ready!**

---

## Next Steps

1. **Build UI Components** - Create controllers, Livewire components, and views
2. **Add Permissions** - Define role-based access (e.g., `manage hr`, `manage library`, `manage transport`)
3. **Implement Business Logic** - Payroll calculations, fine calculations, GPS tracking
4. **Add Reports** - Generate PDF reports for payslips, library usage, transport logs
5. **Integrate External Services** - GPS providers, SMS gateways

---

**Created:** 2025-09-30  
**Version:** 1.0  
**Status:** Database & Models Complete - UI Implementation Pending
