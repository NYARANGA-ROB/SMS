# Localization Setup - Quick Start Guide

## Step-by-Step Setup Instructions

### 1. Run Database Migrations

```bash
php artisan migrate
```

This will add the `preferred_language` column to the users table.

### 2. Update `config/app.php`

Add supported locales configuration:

```php
// config/app.php

'locale' => 'en',
'fallback_locale' => 'en',

// Add this new configuration
'supported_locales' => ['en', 'sw'],
```

### 3. Register Middleware

Update `app/Http/Kernel.php`:

```php
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        
        // Add this line
        \App\Http\Middleware\SetLocale::class,
    ],
];
```

### 4. Add Routes

Add to `routes/web.php`:

```php
// Language switching routes
Route::post('/language/switch', [App\Http\Controllers\LanguageController::class, 'switch'])
    ->name('language.switch');
Route::get('/language/available', [App\Http\Controllers\LanguageController::class, 'getLanguages'])
    ->name('language.available');
```

### 5. Add Language Switcher to Layout

Update `resources/views/layouts/app.blade.php`:

Find the navigation section and add the language switcher:

```blade
{{-- In the navigation bar, before the user dropdown --}}
<div class="flex items-center space-x-4">
    {{-- Language Switcher --}}
    @livewire('language-switcher')
    
    {{-- User dropdown and other nav items --}}
</div>
```

### 6. Update User Model

Add to `app/Models/User.php`:

```php
protected $fillable = [
    // ... existing fields
    'preferred_language',
];
```

### 7. Clear Cache

```bash
php artisan cache:clear
php artisan config:clear
php artisan view:clear
```

### 8. Test the Implementation

#### Test Language Switching:

1. Login to the application
2. Look for the language switcher in the navigation bar (flag icon)
3. Click and select "Kiswahili"
4. UI should update to Kiswahili
5. Switch back to "English"

#### Test Notifications:

```php
// In tinker: php artisan tinker

$student = User::role('student')->first();
$parent = $student->parents->first();

// Set parent language to Kiswahili
$parent->update(['preferred_language' => 'sw']);

// Send test notification
$notificationService = app(\App\Services\NotificationService::class);
$notificationService->notifyAttendance($student, 'present', now());

// Check notification - should be in Kiswahili
$notification = \App\Models\ParentNotification::latest()->first();
echo $notification->message;
```

## Quick Translation Reference

### Adding New Translations

1. **Add to English file** (`lang/en/[category].php`):
```php
'my_new_key' => 'My New Text',
```

2. **Add to Kiswahili file** (`lang/sw/[category].php`):
```php
'my_new_key' => 'Maandishi Yangu Mapya',
```

3. **Use in Blade**:
```blade
{{ __('[category].my_new_key') }}
```

### Translation Categories

- **`parent_portal.php`** - Parent portal specific translations
- **`ui.php`** - General UI elements (buttons, labels, etc.)
- **`notifications.php`** - Notification messages

### Common Translation Patterns

```blade
{{-- Simple translation --}}
{{ __('ui.dashboard') }}

{{-- With parameters --}}
{{ __('notifications.student_marked_present', ['student' => $name, 'date' => $date]) }}

{{-- In attributes --}}
<input placeholder="{{ __('ui.search') }}">

{{-- In directives --}}
@section('title', __('parent_portal.dashboard'))
```

## Verification Checklist

- [ ] Migrations run successfully
- [ ] `config/app.php` updated with supported locales
- [ ] Middleware registered in `Kernel.php`
- [ ] Routes added to `web.php`
- [ ] Language switcher visible in navigation
- [ ] User model includes `preferred_language` in fillable
- [ ] Cache cleared
- [ ] Can switch between English and Kiswahili
- [ ] Parent portal displays in selected language
- [ ] Notifications sent in parent's preferred language
- [ ] Language preference persists across sessions

## Troubleshooting

### Issue: Language switcher not showing
**Solution**: 
- Check if Livewire is properly installed
- Ensure `@livewire('language-switcher')` is in the layout
- Clear view cache: `php artisan view:clear`

### Issue: Translations not working
**Solution**:
- Verify language files exist in `lang/en/` and `lang/sw/`
- Check translation key is correct
- Clear cache: `php artisan cache:clear`
- Check current locale: `dd(app()->getLocale())`

### Issue: Language not persisting
**Solution**:
- Ensure middleware is registered
- Check session is working
- Verify `preferred_language` column exists in users table

### Issue: Notifications in wrong language
**Solution**:
- Check parent's `preferred_language` value
- Verify `NotificationService` sets locale before sending
- Test with: `$parent->update(['preferred_language' => 'sw'])`

## Default Language for New Users

To set default language for new users, update the User factory or registration:

```php
// In registration or user creation
User::create([
    // ... other fields
    'preferred_language' => config('app.locale', 'en'),
]);
```

## SMS/WhatsApp Integration Note

The notification system is ready for SMS and WhatsApp. To enable:

1. **For SMS (Africa's Talking)**:
   - Install: `composer require africastalking/africastalking`
   - Configure in `.env`
   - Uncomment code in `NotificationService::sendSms()`

2. **For WhatsApp (Twilio)**:
   - Install: `composer require twilio/sdk`
   - Configure in `.env`
   - Uncomment code in `NotificationService::sendWhatsApp()`

Messages will automatically be sent in the parent's preferred language.

## Next Steps

1. ✅ Complete setup steps above
2. ✅ Test language switching
3. ✅ Test notifications in both languages
4. 📝 Train staff on language features
5. 📝 Create user guide for parents
6. 📝 Add more translations as needed
7. 📝 Consider adding more languages (French, etc.)

## Support

For issues or questions:
- Check `LOCALIZATION_GUIDE.md` for detailed documentation
- Review Laravel localization docs: https://laravel.com/docs/localization
- Check translation files in `lang/` directory
