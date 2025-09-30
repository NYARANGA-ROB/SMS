# Parent Portal Implementation Guide

## Overview
Comprehensive parent portal with attendance tracking, fee balances, CBC progress reports, announcements, and secure parent-teacher messaging with multi-channel notifications (Email, SMS, WhatsApp).

## Features Implemented

### 1. **Parent Dashboard**
- Overview of all children
- Quick access to attendance, fees, CBC progress
- Recent announcements and notifications
- Unread message count

### 2. **Attendance Tracking**
- View detailed attendance records for each child
- Attendance statistics (present, absent, late)
- Attendance rate calculation
- Date-wise attendance history

### 3. **Fee Balance Management**
- View all fee invoices for each child
- Outstanding balance calculation
- Payment history
- Due date tracking

### 4. **CBC Progress Reports**
- Subject-wise progress tracking
- CBC assessment levels:
  - **EE** (Exceeding Expectations) - 80%+
  - **ME** (Meeting Expectations) - 60-79%
  - **AE** (Approaching Expectations) - 40-59%
  - **BE** (Below Expectations) - <40%
- Teacher comments and feedback
- Strengths and areas for improvement
- Semester-wise grouping

### 5. **Announcements**
- School-wide announcements
- Real-time updates
- Filtered by active dates

### 6. **Secure Messaging System**
- Parent-Teacher direct messaging
- Message threads with replies
- Read/unread status
- Subject-based organization
- Notification on new messages

### 7. **Multi-Channel Notifications**
Integrated notification system supporting:
- **Email** - Full message with formatting
- **SMS** - Short text notifications
- **WhatsApp** - Rich media messages

Notification types:
- Attendance updates
- Fee balance reminders
- CBC progress reports
- New messages
- School announcements

### 8. **Notification Preferences**
Parents can customize notification channels for each type:
- Enable/disable email notifications
- Enable/disable SMS notifications
- Enable/disable WhatsApp notifications
- Per-notification-type granular control

## Database Schema

### Tables Created

#### 1. `messages`
```sql
- id
- sender_id (FK to users)
- receiver_id (FK to users)
- school_id (FK to schools)
- subject
- body
- is_read
- read_at
- parent_message_id (for threading)
- timestamps
- soft_deletes
```

#### 2. `parent_notifications`
```sql
- id
- user_id (FK to users)
- school_id (FK to schools)
- type (attendance, fee, announcement, message, cbc_progress)
- title
- message
- data (JSON)
- is_read
- read_at
- sent_email
- sent_sms
- sent_whatsapp
- timestamps
```

#### 3. `cbc_progress_reports`
```sql
- id
- student_id (FK to users)
- school_id (FK to schools)
- semester_id (FK to semesters)
- subject_id (FK to subjects)
- teacher_id (FK to users)
- learning_outcomes
- exceeding_expectations (count)
- meeting_expectations (count)
- approaching_expectations (count)
- below_expectations (count)
- overall_grade (EE, ME, AE, BE)
- teacher_comments
- strengths
- areas_for_improvement
- timestamps
```

#### 4. `notification_preferences`
```sql
- id
- user_id (FK to users)
- email_attendance, email_fees, email_announcements, email_messages, email_cbc_progress
- sms_attendance, sms_fees, sms_announcements, sms_messages, sms_cbc_progress
- whatsapp_attendance, whatsapp_fees, whatsapp_announcements, whatsapp_messages, whatsapp_cbc_progress
- timestamps
```

## Models Created

1. **Message** - `app/Models/Message.php`
2. **ParentNotification** - `app/Models/ParentNotification.php`
3. **CbcProgressReport** - `app/Models/CbcProgressReport.php`
4. **NotificationPreference** - `app/Models/NotificationPreference.php`

## Services

### NotificationService
**Location:** `app/Services/NotificationService.php`

**Methods:**
- `sendToParent()` - Send notification via all enabled channels
- `sendEmail()` - Email notification
- `sendSms()` - SMS notification (integrate with Africa's Talking, Twilio)
- `sendWhatsApp()` - WhatsApp notification (integrate with WhatsApp Business API)
- `notifyAttendance()` - Notify about attendance
- `notifyFeeBalance()` - Notify about fee balance
- `notifyCbcProgress()` - Notify about CBC progress
- `notifyNewMessage()` - Notify about new message

## Controllers

### ParentPortalController
**Location:** `app/Http/Controllers/ParentPortalController.php`

**Routes:**
- `GET /parent/dashboard` - Parent dashboard
- `GET /parent/attendance/{student}` - View student attendance
- `GET /parent/fee-balances/{student}` - View fee balances
- `GET /parent/cbc-progress/{student}` - View CBC progress
- `GET /parent/announcements` - View announcements
- `GET /parent/messages` - View messages
- `GET /parent/messages/{id}` - View message thread
- `GET /parent/notifications` - View notifications

## Livewire Components

1. **SendMessageForm** - `app/Livewire/SendMessageForm.php`
   - Send new messages
   - Reply to messages
   - Auto-load teachers for parent's children

2. **NotificationPreferencesForm** - `app/Livewire/NotificationPreferencesForm.php`
   - Manage notification preferences
   - Toggle email, SMS, WhatsApp per notification type

## Views Created

1. `resources/views/pages/parent-portal/dashboard.blade.php` - Main dashboard
2. Additional views needed (create these):
   - `attendance.blade.php`
   - `fee-balances.blade.php`
   - `cbc-progress.blade.php`
   - `announcements.blade.php`
   - `messages.blade.php`
   - `message-thread.blade.php`
   - `notifications.blade.php`

3. Livewire views:
   - `resources/views/livewire/send-message-form.blade.php`
   - `resources/views/livewire/notification-preferences-form.blade.php`

4. Email template:
   - `resources/views/emails/parent-notification.blade.php`

## Routes to Add

Add to `routes/web.php`:

```php
// Parent Portal Routes
Route::middleware(['auth', 'role:parent'])->prefix('parent')->name('parent.')->group(function () {
    Route::get('/dashboard', [ParentPortalController::class, 'dashboard'])->name('dashboard');
    Route::get('/attendance/{student}', [ParentPortalController::class, 'attendance'])->name('attendance');
    Route::get('/fee-balances/{student}', [ParentPortalController::class, 'feeBalances'])->name('fee-balances');
    Route::get('/cbc-progress/{student}', [ParentPortalController::class, 'cbcProgress'])->name('cbc-progress');
    Route::get('/announcements', [ParentPortalController::class, 'announcements'])->name('announcements');
    Route::get('/messages', [ParentPortalController::class, 'messages'])->name('messages');
    Route::get('/messages/{id}', [ParentPortalController::class, 'showMessage'])->name('messages.show');
    Route::get('/notifications', [ParentPortalController::class, 'notifications'])->name('notifications');
});
```

## Menu Integration

Add to `app/Livewire/Layouts/Menu.php`:

```php
[
    'type' => 'menu-item',
    'text' => 'Parent Portal',
    'icon' => 'fas fa-home',
    'can'  => 'role:parent',
    'submenu' => [
        [
            'type'  => 'menu-item',
            'text'  => 'Dashboard',
            'route' => 'parent.dashboard',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'Messages',
            'route' => 'parent.messages',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'Announcements',
            'route' => 'parent.announcements',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'Notifications',
            'route' => 'parent.notifications',
        ],
    ],
],
```

## Third-Party Integration Setup

### SMS Integration (Africa's Talking)

1. Install package:
```bash
composer require africastalking/africastalking
```

2. Add to `.env`:
```env
AFRICASTALKING_USERNAME=your_username
AFRICASTALKING_API_KEY=your_api_key
AFRICASTALKING_FROM=your_shortcode
```

3. Uncomment SMS code in `NotificationService::sendSms()`

### WhatsApp Integration (Twilio)

1. Install package:
```bash
composer require twilio/sdk
```

2. Add to `.env`:
```env
TWILIO_SID=your_sid
TWILIO_TOKEN=your_token
TWILIO_WHATSAPP_FROM=whatsapp:+14155238886
```

3. Uncomment WhatsApp code in `NotificationService::sendWhatsApp()`

## Usage Examples

### Send Notification to Parent

```php
use App\Services\NotificationService;

$notificationService = app(NotificationService::class);

// Notify about attendance
$notificationService->notifyAttendance($student, 'absent', now());

// Notify about fee balance
$notificationService->notifyFeeBalance($student, 5000, '2025-10-15');

// Notify about CBC progress
$notificationService->notifyCbcProgress($student, 'Mathematics', 'EE');
```

### Create CBC Progress Report

```php
use App\Models\CbcProgressReport;

$report = CbcProgressReport::create([
    'student_id' => $student->id,
    'school_id' => $school->id,
    'semester_id' => $semester->id,
    'subject_id' => $subject->id,
    'teacher_id' => $teacher->id,
    'exceeding_expectations' => 5,
    'meeting_expectations' => 3,
    'approaching_expectations' => 2,
    'below_expectations' => 0,
    'teacher_comments' => 'Excellent progress in mathematics',
    'strengths' => 'Problem solving, logical thinking',
    'areas_for_improvement' => 'Speed in calculations',
]);

$report->overall_grade = $report->calculateOverallGrade();
$report->save();
```

## Security Features

1. **Access Control** - Parents can only view their own children's data
2. **Role-based Access** - Routes protected by `role:parent` middleware
3. **Message Privacy** - Messages only visible to sender and receiver
4. **Secure Messaging** - No direct email exposure between parents and teachers
5. **Audit Trail** - All messages and notifications timestamped

## Next Steps

1. Run migration: `php artisan migrate`
2. Create remaining blade views
3. Add routes to `web.php`
4. Update menu in `Menu.php`
5. Configure SMS/WhatsApp providers
6. Test notification system
7. Create seeder for sample data

## Testing

Create test data:
```php
// Create notification preferences for a parent
$parent = User::role('parent')->first();
NotificationPreference::create(['user_id' => $parent->id]);

// Send test notification
$notificationService->notifyAttendance($student, 'present', now());
```

## Notes

- All monetary values should be in the school's currency (KES by default)
- CBC grading follows Kenyan curriculum standards
- Notifications are queued for better performance (consider using Laravel Queues)
- Message attachments can be added as future enhancement
- Real-time notifications can be added using Laravel Echo and Pusher
