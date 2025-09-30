# Multi-Language Support Implementation Guide

## Overview
Complete localization system supporting English and Kiswahili for UI and parental communications using Laravel's built-in localization features.

## Supported Languages

1. **English (en)** - Default language
2. **Kiswahili (sw)** - Kenyan national language

## Features Implemented

### 1. **Language Files Structure**

```
lang/
├── en/
│   ├── parent_portal.php    # Parent portal translations
│   ├── ui.php                # General UI translations
│   └── notifications.php     # Notification messages
└── sw/
    ├── parent_portal.php    # Kiswahili parent portal
    ├── ui.php                # Kiswahili UI
    └── notifications.php     # Kiswahili notifications
```

### 2. **Translation Categories**

#### Parent Portal (`parent_portal.php`)
- Dashboard elements
- Attendance tracking
- Fee balances
- CBC progress reports
- Messages and notifications
- Common actions

#### UI Elements (`ui.php`)
- Navigation items
- Action buttons (Add, Edit, Delete, etc.)
- Status labels
- Form labels
- Messages and alerts
- Language selector

#### Notifications (`notifications.php`)
- Attendance notifications
- Fee balance reminders
- CBC progress updates
- Message notifications
- Announcement notifications
- Email templates

### 3. **User Language Preferences**

Each user can set their preferred language which is:
- Stored in `users.preferred_language` column
- Persisted across sessions
- Applied to all notifications
- Used for email communications

### 4. **Language Middleware**

**SetLocale Middleware** (`app/Http/Middleware/SetLocale.php`)

Priority order for locale detection:
1. User's saved preference (if authenticated)
2. Session locale
3. Browser Accept-Language header
4. Default locale (English)

### 5. **Language Switcher Component**

**Livewire Component** (`app/Livewire/LanguageSwitcher.php`)
- Dropdown selector with flags
- Saves to user preferences
- Updates session
- Refreshes page to apply translations

## Database Changes

### Migration: `add_preferred_language_to_users_table`

```sql
ALTER TABLE users ADD COLUMN preferred_language VARCHAR(5) DEFAULT 'en';
```

## Usage Examples

### In Blade Templates

```blade
{{-- Simple translation --}}
<h1>{{ __('parent_portal.dashboard') }}</h1>

{{-- Translation with parameters --}}
<p>{{ __('notifications.student_marked_present', ['student' => $name, 'date' => $date]) }}</p>

{{-- Pluralization --}}
<p>{{ trans_choice('ui.students', $count) }}</p>

{{-- Check current locale --}}
@if(app()->getLocale() === 'sw')
    <p>Karibu!</p>
@endif
```

### In Controllers

```php
// Get translated string
$message = __('parent_portal.attendance');

// With parameters
$message = __('notifications.fee_balance_message', [
    'student' => $student->name,
    'currency' => 'KES',
    'amount' => number_format($balance, 2)
]);

// Set locale temporarily
app()->setLocale('sw');
$message = __('notifications.attendance_update');
```

### In Notifications (Multi-language)

```php
// NotificationService automatically uses parent's preferred language
$notificationService = app(NotificationService::class);

// This will send in parent's preferred language
$notificationService->notifyAttendance($student, 'present', now());
$notificationService->notifyFeeBalance($student, 5000, '2025-10-15');
$notificationService->notifyCbcProgress($student, 'Mathematics', 'EE');
```

## Configuration

### Update `config/app.php`

```php
'locale' => 'en',
'fallback_locale' => 'en',
'supported_locales' => ['en', 'sw'],
```

### Register Middleware

Add to `app/Http/Kernel.php`:

```php
protected $middlewareGroups = [
    'web' => [
        // ... other middleware
        \App\Http\Middleware\SetLocale::class,
    ],
];
```

### Add Routes

Add to `routes/web.php`:

```php
// Language switching
Route::post('/language/switch', [LanguageController::class, 'switch'])->name('language.switch');
Route::get('/language/available', [LanguageController::class, 'getLanguages'])->name('language.available');
```

## Adding the Language Switcher to Layout

### In `resources/views/layouts/app.blade.php`

Add to navigation bar:

```blade
<div class="flex items-center space-x-4">
    {{-- Other nav items --}}
    
    {{-- Language Switcher --}}
    @livewire('language-switcher')
</div>
```

## Adding New Translations

### 1. Add to English File

```php
// lang/en/parent_portal.php
return [
    'new_feature' => 'New Feature',
    'feature_description' => 'This is a :feature',
];
```

### 2. Add Kiswahili Translation

```php
// lang/sw/parent_portal.php
return [
    'new_feature' => 'Kipengele Kipya',
    'feature_description' => 'Hiki ni :feature',
];
```

### 3. Use in Blade

```blade
<h1>{{ __('parent_portal.new_feature') }}</h1>
<p>{{ __('parent_portal.feature_description', ['feature' => 'example']) }}</p>
```

## Translation Keys Reference

### Common UI Actions

| English | Kiswahili | Key |
|---------|-----------|-----|
| Add | Ongeza | `ui.add` |
| Edit | Hariri | `ui.edit` |
| Delete | Futa | `ui.delete` |
| View | Tazama | `ui.view` |
| Save | Hifadhi | `ui.save` |
| Cancel | Ghairi | `ui.cancel` |
| Search | Tafuta | `ui.search` |

### Parent Portal

| English | Kiswahili | Key |
|---------|-----------|-----|
| Parent Portal | Mlango wa Mzazi | `parent_portal.dashboard` |
| My Children | Watoto Wangu | `parent_portal.my_children` |
| Attendance | Mahudhurio | `parent_portal.attendance` |
| Fee Balances | Salio la Ada | `parent_portal.fee_balances` |
| CBC Progress | Maendeleo ya CBC | `parent_portal.cbc_progress` |
| Messages | Ujumbe | `parent_portal.messages` |

### Notification Messages

| English | Kiswahili | Key |
|---------|-----------|-----|
| Attendance Update | Arifa ya Mahudhurio | `notifications.attendance_update` |
| Fee Balance Reminder | Ukumbusho wa Salio la Ada | `notifications.fee_balance_reminder` |
| CBC Progress Report | Ripoti ya Maendeleo ya CBC | `notifications.cbc_progress_update` |
| New Message | Ujumbe Mpya | `notifications.new_message` |

## Email Templates with Localization

### Create Localized Email Template

```blade
{{-- resources/views/emails/parent-notification.blade.php --}}
<!DOCTYPE html>
<html>
<head>
    <title>{{ $title }}</title>
</head>
<body>
    <h2>{{ __('notifications.dear_parent') }}</h2>
    
    <p>{{ $message }}</p>
    
    @if(isset($data['view_url']))
        <a href="{{ $data['view_url'] }}">{{ __('notifications.view_details') }}</a>
    @endif
    
    <p>{{ __('notifications.regards') }},<br>
    {{ __('notifications.school_administration') }}</p>
</body>
</html>
```

## SMS/WhatsApp Localization

The `NotificationService` automatically uses the parent's preferred language:

```php
// In NotificationService
protected function sendSms(User $user, string $message)
{
    // Message is already translated based on user's preferred_language
    // Send via SMS provider
}

protected function sendWhatsApp(User $user, string $message)
{
    // Message is already translated based on user's preferred_language
    // Send via WhatsApp API
}
```

## Testing Translations

### Test Language Switching

```php
// In Tinker or test
$user = User::find(1);

// Switch to Kiswahili
$user->update(['preferred_language' => 'sw']);
app()->setLocale('sw');

// Test translation
echo __('parent_portal.dashboard'); // Output: Mlango wa Mzazi

// Switch to English
$user->update(['preferred_language' => 'en']);
app()->setLocale('en');

echo __('parent_portal.dashboard'); // Output: Parent Portal
```

### Test Notifications

```php
$student = User::role('student')->first();
$parent = $student->parents->first();

// Set parent's language to Kiswahili
$parent->update(['preferred_language' => 'sw']);

// Send notification (will be in Kiswahili)
$notificationService = app(NotificationService::class);
$notificationService->notifyAttendance($student, 'present', now());
```

## Best Practices

### 1. **Always Use Translation Keys**
```blade
{{-- Bad --}}
<button>Add Student</button>

{{-- Good --}}
<button>{{ __('ui.add') }} {{ __('ui.students') }}</button>
```

### 2. **Use Parameters for Dynamic Content**
```blade
{{-- Bad --}}
<p>{{ __('messages.welcome') }} {{ $name }}</p>

{{-- Good --}}
<p>{{ __('messages.welcome_user', ['name' => $name]) }}</p>
```

### 3. **Keep Keys Organized**
- Group related translations in the same file
- Use descriptive key names
- Maintain consistent naming conventions

### 4. **Test Both Languages**
- Always add translations for both languages
- Test UI with both languages
- Verify notifications in both languages

### 5. **Handle Pluralization**
```php
// lang/en/ui.php
'students' => '{0} No students|{1} One student|[2,*] :count students',

// lang/sw/ui.php
'students' => '{0} Hakuna wanafunzi|{1} Mwanafunzi mmoja|[2,*] Wanafunzi :count',
```

## Adding More Languages

To add a new language (e.g., French):

1. Create language directory: `lang/fr/`
2. Copy and translate all files from `lang/en/`
3. Update `config/app.php`:
   ```php
   'supported_locales' => ['en', 'sw', 'fr'],
   ```
4. Add to `LanguageSwitcher.php`:
   ```php
   public $languages = [
       'en' => ['name' => 'English', 'flag' => '🇬🇧'],
       'sw' => ['name' => 'Kiswahili', 'flag' => '🇰🇪'],
       'fr' => ['name' => 'Français', 'flag' => '🇫🇷'],
   ];
   ```

## Troubleshooting

### Translations Not Showing

1. **Clear cache**: `php artisan cache:clear`
2. **Check locale**: `dd(app()->getLocale())`
3. **Verify file exists**: Check `lang/{locale}/` directory
4. **Check key**: Ensure translation key exists

### Language Not Persisting

1. **Check middleware**: Ensure `SetLocale` is registered
2. **Verify session**: Check session is working
3. **Check user preference**: Verify `preferred_language` column exists

### Notifications in Wrong Language

1. **Check user preference**: `$user->preferred_language`
2. **Verify NotificationService**: Ensure locale is set before translation
3. **Test manually**: Set locale and check translation output

## Migration Checklist

- [x] Create language files (en, sw)
- [x] Add `preferred_language` column to users table
- [x] Create SetLocale middleware
- [x] Create LanguageController
- [x] Create LanguageSwitcher component
- [x] Update NotificationService for multi-language
- [x] Update parent portal views with translations
- [ ] Register middleware in Kernel
- [ ] Add routes for language switching
- [ ] Add language switcher to layout
- [ ] Update config/app.php
- [ ] Run migrations
- [ ] Test both languages
- [ ] Train users on language switching

## Resources

- [Laravel Localization Docs](https://laravel.com/docs/localization)
- [Kiswahili Translation Guide](https://www.swahililanguage.com/)
- Translation keys are in `lang/` directory
- Component code in `app/Livewire/LanguageSwitcher.php`
