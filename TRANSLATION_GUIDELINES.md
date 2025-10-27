# Translation & Localization Guidelines

This document provides comprehensive guidelines for translating and localizing the Mentor LMS application, covering both frontend (React/TypeScript) and backend (Laravel) components.

## Table of Contents

1. [Overview](#overview)
2. [Backend Translation System](#backend-translation-system)
3. [Frontend Translation System](#frontend-translation-system)
4. [Language Module Management](#language-module-management)
5. [Adding New Languages](#adding-new-languages)
6. [Translation File Structure](#translation-file-structure)
7. [Best Practices](#best-practices)
8. [Troubleshooting](#troubleshooting)

## Overview

Mentor LMS uses a comprehensive translation system that supports:
- **Multi-language support** with database-driven translations
- **RTL/LTR direction** support
- **Frontend and backend** translation management
- **Dynamic language switching** without page reload
- **Admin interface** for translation management

### Key Components

- **Backend**: Laravel's translation system with custom Language module
- **Frontend**: React components with TypeScript definitions
- **Database**: Language and LanguageProperty models for dynamic translations
- **Admin Interface**: Translation management dashboard

## Backend Translation System

### File Locations

#### Core Translation Files
```
lang/
├── en/                    # Default English translations
│   ├── auth.php          # Authentication messages
│   ├── button.php        # Button labels
│   ├── common.php        # Common terms
│   ├── dashboard.php     # Dashboard content
│   ├── frontend.php      # Frontend-specific content
│   ├── input.php         # Form input labels
│   ├── settings.php      # Settings page content
│   └── table.php         # Table headers and actions
```

#### Language Module Files
```
Modules/Language/
├── app/
│   ├── Http/Controllers/
│   │   └── LanguageController.php    # Language management
│   ├── Models/
│   │   ├── Language.php              # Language model
│   │   └── LanguageProperty.php      # Translation properties
│   └── Services/
│       └── LanguageService.php       # Translation logic
```

#### Storage Files
```
storage/app/lang/
├── default/              # Default language files (copied when adding new language)
└── groups/               # Translation group definitions
    ├── auth.php
    ├── button.php
    ├── common.php
    ├── dashboard.php
    ├── frontend.php
    ├── input.php
    └── settings.php
```

### How Backend Translations Work

1. **Translation Loading**: Translations are loaded via `HandleInertiaRequests` middleware
2. **Dynamic Loading**: Language properties are cached and loaded dynamically
3. **Group-based**: Translations are organized into logical groups (auth, common, etc.)
4. **Database Storage**: Custom translations are stored in `language_properties` table

### Using Translations in Backend

```php
// In controllers, services, or views
trans('common.save')           // Returns translated text
trans('auth.login')           // Authentication messages
trans('button.submit')        // Button labels
trans('frontend.courses')     // Frontend content
```

### Adding New Translation Keys

1. **Add to group files** in `storage/app/lang/groups/`
2. **Add to language files** in `lang/en/`
3. **Update database** via admin interface or migration

## Frontend Translation System

### File Locations

#### TypeScript Definitions
```
resources/js/types/lang/
├── frontend.d.ts          # Frontend-specific translations
├── common.d.ts           # Common translations
├── dashboard.d.ts        # Dashboard translations
├── settings.d.ts         # Settings translations
└── button.d.ts           # Button translations
```

#### Translation Management
```
resources/js/pages/dashboard/settings/translation/
├── index.tsx             # Language list management
├── update.tsx            # Edit translations
├── properties.tsx        # Edit specific properties
└── partials/
    └── add-language.tsx  # Add new language
```

#### Language Components
```
resources/js/
├── components/language.tsx    # Language switcher component
└── hooks/use-lang.tsx         # Translation hook
```

### How Frontend Translations Work

1. **Shared Data**: Translations are passed via Inertia.js shared data
2. **TypeScript Support**: Full type safety for translation keys
3. **Hook-based**: `useLang()` hook provides access to translations
4. **Component Integration**: Language switcher for dynamic switching

### Using Translations in Frontend

```tsx
import { useLang } from '@/hooks/use-lang';

const MyComponent = () => {
  const translate = useLang();
  
  return (
    <div>
      <h1>{translate.common.title}</h1>
      <button>{translate.button.save}</button>
    </div>
  );
};
```

### Translation Hook Usage

```tsx
// Access all translations
const translate = useLang();

// Access specific groups
const { common, button, frontend } = translate;

// Use in JSX
<h1>{translate.dashboard.welcome}</h1>
<button>{translate.button.submit}</button>
```

## Language Module Management

### Admin Interface

Access translation management at: `/dashboard/settings/translation`

#### Features Available:
- **Language List**: View all available languages
- **Add Language**: Create new language with translations
- **Edit Translations**: Modify existing translations
- **Set Default**: Change default language
- **Enable/Disable**: Toggle language availability
- **Direction Control**: Switch between LTR/RTL

### Language Controller Methods

```php
// Available routes and methods
Route::get('/translation', [LanguageController::class, 'index']);
Route::post('/translation', [LanguageController::class, 'store']);
Route::put('/translation/{id}', [LanguageController::class, 'update']);
Route::get('/translation/{local}/edit', [LanguageController::class, 'edit']);
Route::put('/translation/property/{id}', [LanguageController::class, 'update_property']);
Route::post('/translation/{id}/default', [LanguageController::class, 'default']);
Route::post('/change-lang', [LanguageController::class, 'change_lang']);
Route::post('/change-direction', [LanguageController::class, 'change_direction']);
```

## Adding New Languages

### Step 1: Add Language via Admin Interface

1. Go to `/dashboard/settings/translation`
2. Click "Add Language" button
3. Fill in language details:
   - **Name**: Display name (e.g., "Spanish")
   - **Code**: ISO code (e.g., "es")
   - **Flag**: Flag emoji or image
   - **Native Name**: Native language name (e.g., "Español")

### Step 2: Translate Content

1. Click edit button for the new language
2. Translate each group:
   - **Auth**: Authentication messages
   - **Button**: Button labels
   - **Common**: Common terms
   - **Dashboard**: Dashboard content
   - **Frontend**: Frontend-specific content
   - **Input**: Form input labels
   - **Settings**: Settings page content

### Step 3: Enable Language

1. Toggle the language switch to enable
2. Optionally set as default language

### Manual Language Addition

If you need to add languages manually:

```php
// Create language directory
mkdir lang/es

// Copy default files
cp -r lang/en/* lang/es/

// Translate the files in lang/es/
```

## Translation File Structure

### Backend Translation Groups

Each translation group follows this structure:

```php
// storage/app/lang/groups/frontend.php
return [
    [
        'name' => 'Group Name',
        'slug' => 'group_slug',
        'properties' => [
            'key' => 'Translation Value',
            'another_key' => 'Another Translation',
        ]
    ],
    // ... more groups
];
```

### Frontend TypeScript Definitions

```typescript
// resources/js/types/lang/frontend.d.ts
interface FrontendLang {
  // Group properties
  replies: string;
  posting: string;
  something_went_wrong: string;
  // ... more properties
}
```

### Language Model Structure

```php
// Language model fields
[
    'name' => 'Language Name',
    'code' => 'language_code',
    'flag' => 'flag_emoji',
    'nativeName' => 'Native Name',
    'is_active' => true,
    'is_default' => false,
]
```

## Best Practices

### 1. Translation Key Naming

- Use **snake_case** for keys
- Use **descriptive names** that indicate context
- Group related keys together
- Use consistent prefixes for similar content

```php
// Good examples
'course_title' => 'Course Title',
'course_description' => 'Course Description',
'course_price' => 'Course Price',

// Bad examples
'title' => 'Course Title',  // Too generic
'courseTitle' => 'Course Title',  // Wrong case
```

### 2. Translation Organization

- **Group by functionality**: auth, common, dashboard, etc.
- **Keep related translations together**
- **Use consistent terminology** across groups
- **Document special cases** in comments

### 3. Frontend Usage

- **Always use the translation hook**: `useLang()`
- **Type your translations**: Use TypeScript definitions
- **Handle missing translations gracefully**
- **Test with different languages**

### 4. RTL Support

- **Test with RTL languages** (Arabic, Hebrew)
- **Use CSS logical properties** where possible
- **Consider text direction** in UI layouts
- **Test language switching** functionality

### 5. Performance Considerations

- **Translations are cached** for performance
- **Clear cache** when updating translations
- **Use database translations** for dynamic content
- **Minimize translation file sizes**

## Troubleshooting

### Common Issues

#### 1. Translations Not Loading

**Symptoms**: Text shows as translation keys instead of translated text

**Solutions**:
- Check if language is active in database
- Verify translation files exist
- Clear application cache: `php artisan cache:clear`
- Check middleware configuration

#### 2. Missing Translation Keys

**Symptoms**: "Translation key not found" errors

**Solutions**:
- Add missing keys to group files
- Update language files
- Check TypeScript definitions
- Verify database entries

#### 3. Language Switching Not Working

**Symptoms**: Language doesn't change when switching

**Solutions**:
- Check cookie settings
- Verify middleware is applied
- Clear browser cookies
- Check route configuration

#### 4. RTL Layout Issues

**Symptoms**: Layout breaks with RTL languages

**Solutions**:
- Check CSS direction properties
- Test with RTL-specific styles
- Verify HTML dir attribute
- Use logical CSS properties

### Debugging Commands

```bash
# Clear all caches
php artisan cache:clear
php artisan config:clear
php artisan route:clear
php artisan view:clear

# Check language configuration
php artisan tinker
>>> app('system_settings')
>>> App::getLocale()

# Check database translations
php artisan tinker
>>> Modules\Language\Models\Language::with('properties')->get()
```

### File Permissions

Ensure proper permissions for language files:

```bash
# Set correct permissions
chmod -R 755 lang/
chmod -R 755 storage/app/lang/
```

## Additional Resources

- **Laravel Localization**: https://laravel.com/docs/localization
- **Inertia.js Shared Data**: https://inertiajs.com/shared-data
- **React Internationalization**: https://react.i18next.com/
- **RTL Support Guide**: https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Writing_Modes

---

**Note**: This system is designed to be flexible and maintainable. Always test translations thoroughly and consider the user experience when implementing new languages or modifying existing translations.
