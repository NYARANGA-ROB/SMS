# Communication Module Documentation

## Overview
The communication module provides comprehensive tools for school-wide messaging, event management, and an AI-powered chatbot assistant. It enables bulk announcements via SMS, WhatsApp, and Email, calendar management for exams and events, and instant student queries.

## Features Implemented

### 1. Bulk Announcements System
**Routes:** `/dashboard/announcements` (list), `/dashboard/announcements/create` (create)  
**Livewire Components:** `CreateAnnouncementForm`, `ListAnnouncements`  
**Model:** `Announcement`

**Features:**
- **Multi-channel delivery**: Send via Email, SMS, and WhatsApp simultaneously
- **Targeted messaging**: Filter by roles (students, parents, teachers) and sections
- **Scheduling**: Send immediately or schedule for later
- **Recipient tracking**: Automatic counting of recipients, sent, and failed messages
- **Status management**: Draft, Scheduled, Sent, Failed states

**Channels:**
- 📧 **Email** - Uses Laravel Mail system
- 📱 **SMS** - Ready for integration with Twilio, Nexmo, or Africa's Talking
- 💬 **WhatsApp** - Ready for WhatsApp Business API integration

### 2. Event Calendar System
**Routes:** 
- `/dashboard/events` (list)
- `/dashboard/events/create` (create)
- `/dashboard/events/calendar` (calendar view)

**Livewire Components:** `CreateEventForm`, `ListEvents`, `EventCalendar`  
**Model:** `Event`

**Features:**
- **Event types**: Exam, Parents' Day, Holiday, Meeting, Sports, Other
- **Calendar visualization**: Interactive FullCalendar.js integration
- **Date/time management**: Start and end dates with location
- **Visibility control**: Public events or role-specific (students, parents, teachers)
- **Multiple views**: Month, week, and list views

**Event Types:**
- 📝 **Exam** - Scheduled examinations
- 👨‍👩‍👧 **Parents' Day** - Parent-teacher meetings
- 🎉 **Holiday** - School holidays
- 💼 **Meeting** - Staff or student meetings
- ⚽ **Sports** - Sports events and competitions
- 📌 **Other** - General events

### 3. AI Chatbot Assistant
**Livewire Component:** `ChatbotWidget`  
**Service:** `ChatbotService`  
**Model:** `ChatConversation`

**Features:**
- **Floating chat widget**: Always accessible from any page
- **Natural language processing**: Detects user intent from questions
- **Context-aware responses**: Personalized based on user role
- **Conversation logging**: All interactions saved for analytics

**Supported Queries:**
- 💰 **Fee Balance** - "What's my fee balance?" / "Do I owe any fees?"
- 📊 **Attendance** - "What's my attendance?" / "How many days was I present?"
- 📈 **Grades** - "What are my grades?" / "What's my average score?"
- 📅 **Events** - "What events are coming up?" / "When is parents' day?"
- 📚 **Timetable** - "Show my timetable" / "What's my schedule?"
- ❓ **Help** - "Help" / "What can you do?"

## Usage Instructions

### For Administrators

#### Creating Announcements
1. **Navigate to Academics → Announcements → Create Announcement**
2. Fill in the form:
   - **Title**: Brief subject line
   - **Message**: Full announcement text
   - **Channels**: Select Email, SMS, and/or WhatsApp
   - **Target Recipients**: Choose students, parents, and/or teachers
   - **Filter by Sections**: Optionally limit to specific classes
   - **Send Now or Schedule**: Immediate or scheduled delivery
3. Click **Send Now** or **Schedule**

#### Managing Events
1. **Navigate to Academics → Events & Calendar → Create Event**
2. Fill in the form:
   - **Title**: Event name
   - **Type**: Select event category
   - **Start/End Date**: Date and time
   - **Location**: Venue (optional)
   - **Description**: Event details
   - **Visibility**: Public or role-specific
3. Click **Create Event**

#### Viewing Calendar
- **Navigate to Academics → Events & Calendar → Calendar View**
- Switch between month, week, and list views
- Click events for details

### For Students & Parents

#### Using the Chatbot
1. **Click the blue chat button** in the bottom-right corner
2. **Type your question** naturally:
   - "What's my fee balance?"
   - "Show my attendance"
   - "What are my grades?"
   - "What events are coming up?"
3. **Get instant answers** from the AI assistant

#### Viewing Events
- Navigate to **Events & Calendar → Calendar View**
- See all upcoming exams, parents' days, and school events

## Technical Details

### Database Tables

**announcements**
- `id`, `school_id`, `user_id`, `title`, `message`
- `channels` (JSON: email, sms, whatsapp)
- `target_roles` (JSON: student, parent, teacher)
- `target_sections` (JSON: section IDs)
- `status` (draft, scheduled, sent, failed)
- `scheduled_at`, `sent_at`
- `recipients_count`, `sent_count`, `failed_count`

**events**
- `id`, `school_id`, `user_id`, `title`, `description`
- `type` (exam, parents_day, holiday, meeting, sports, other)
- `start_date`, `end_date`, `location`
- `target_roles` (JSON), `is_public`

**chat_conversations**
- `id`, `user_id`, `user_message`, `bot_response`
- `intent` (fee_balance, attendance, grades, etc.)
- `context` (JSON: additional data)

### Models
- `App\Models\Announcement`
- `App\Models\Event`
- `App\Models\ChatConversation`

### Services
- `App\Services\Chatbot\ChatbotService` - AI query processing

### Controllers
- `App\Http\Controllers\AnnouncementController`
- `App\Http\Controllers\EventController`

### Livewire Components
- `App\Livewire\CreateAnnouncementForm`
- `App\Livewire\ListAnnouncements`
- `App\Livewire\CreateEventForm`
- `App\Livewire\ListEvents`
- `App\Livewire\EventCalendar`
- `App\Livewire\ChatbotWidget`

### Routes
```php
// Announcements
GET /dashboard/announcements
GET /dashboard/announcements/create

// Events
GET /dashboard/events
GET /dashboard/events/create
GET /dashboard/events/calendar
```

### External Libraries
- **FullCalendar.js 6.1.10** - Calendar visualization (loaded via CDN)

## SMS/WhatsApp/Email Integration

### Email (Built-in)
Email is ready to use with Laravel's mail system. Configure in `.env`:
```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=your_username
MAIL_PASSWORD=your_password
```

### SMS Integration (Ready for setup)
To enable SMS, integrate with a provider:

**Option 1: Twilio**
```bash
composer require twilio/sdk
```

**Option 2: Africa's Talking**
```bash
composer require africastalking/africastalking
```

Add credentials to `.env` and implement in `AnnouncementService`.

### WhatsApp Integration (Ready for setup)
To enable WhatsApp, use WhatsApp Business API:

**Option 1: Twilio WhatsApp**
```bash
composer require twilio/sdk
```

**Option 2: WhatsApp Cloud API**
Use Facebook's official API with HTTP client.

Add implementation in `AnnouncementService` to send messages.

## Chatbot Intelligence

The chatbot uses **intent detection** with keyword matching:

**Intent Detection Patterns:**
```php
'fee_balance' => ['fee', 'balance', 'payment', 'owe', 'debt']
'attendance' => ['attendance', 'present', 'absent', 'late']
'grades' => ['grade', 'marks', 'score', 'result', 'exam']
'events' => ['event', 'calendar', 'parents day']
```

**Response Generation:**
- Queries database for real-time data
- Formats responses with emojis for clarity
- Logs all conversations for analytics

## Extending the System

### Adding New Chatbot Intents
Edit `app/Services/Chatbot/ChatbotService.php`:

```php
protected function detectIntent(string $message): string
{
    $patterns = [
        // Add new intent
        'library' => ['library', 'book', 'borrow'],
    ];
}

protected function generateResponse(string $intent, string $message): array
{
    return match($intent) {
        'library' => $this->handleLibrary(),
        // ...
    };
}

protected function handleLibrary(): array
{
    // Implement library query logic
}
```

### Adding New Event Types
Edit the migration or add to enum in `Event` model.

### Customizing Announcement Templates
Create reusable templates in the announcement form for common messages.

## Security & Permissions

All features use the existing `read student` permission for access control. Adjust permissions in:
- `app/Livewire/Layouts/Menu.php` - Menu visibility
- Controllers - Route authorization

## Performance Considerations

- **Bulk messaging**: Consider queuing for large recipient lists
- **Calendar loading**: Events are filtered by school and role
- **Chatbot**: Responses are instant (no external API calls)

## Future Enhancements

1. **Queue system** for bulk announcements
2. **Email templates** with rich HTML formatting
3. **SMS delivery reports** and tracking
4. **Advanced chatbot** with machine learning
5. **Push notifications** for mobile apps
6. **Event reminders** via email/SMS
7. **Announcement analytics** (open rates, click rates)
8. **Multi-language support** for chatbot

---

**Created:** 2025-09-30  
**Version:** 1.0
