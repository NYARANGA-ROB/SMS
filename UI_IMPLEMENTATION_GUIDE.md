# UI Implementation Guide for Optional Modules

## Overview
This guide provides the complete structure for implementing UI components for HR & Payroll, Library Management, and Transport Management modules.

---

## File Structure

### HR & Payroll Module

**Controllers:**
- ✅ `app/Http/Controllers/HRController.php` - Created

**Livewire Components:**
- ✅ `app/Livewire/CreateStaffProfileForm.php` - Created
- `app/Livewire/ListStaffProfiles.php`
- `app/Livewire/CreateLeaveForm.php`
- `app/Livewire/ListLeaves.php`
- `app/Livewire/CreatePayrollForm.php`
- `app/Livewire/ListPayrolls.php`
- `app/Livewire/ViewPayslip.php`

**Views:**
- `resources/views/pages/hr/staff/index.blade.php`
- `resources/views/pages/hr/staff/create.blade.php`
- `resources/views/pages/hr/leaves/index.blade.php`
- `resources/views/pages/hr/leaves/create.blade.php`
- `resources/views/pages/hr/payroll/index.blade.php`
- `resources/views/pages/hr/payroll/create.blade.php`

**Livewire Views:**
- `resources/views/livewire/create-staff-profile-form.blade.php`
- `resources/views/livewire/list-staff-profiles.blade.php`
- `resources/views/livewire/create-leave-form.blade.php`
- `resources/views/livewire/list-leaves.blade.php`
- `resources/views/livewire/create-payroll-form.blade.php`
- `resources/views/livewire/list-payrolls.blade.php`

### Library Module

**Controllers:**
- ✅ `app/Http/Controllers/LibraryController.php` - Created

**Livewire Components:**
- `app/Livewire/CreateBookForm.php`
- `app/Livewire/ListBooks.php`
- `app/Livewire/BorrowBookForm.php`
- `app/Livewire/ListBorrowRecords.php`
- `app/Livewire/CreateELibraryResourceForm.php`
- `app/Livewire/ListELibraryResources.php`

**Views:**
- `resources/views/pages/library/books/index.blade.php`
- `resources/views/pages/library/books/create.blade.php`
- `resources/views/pages/library/borrow/index.blade.php`
- `resources/views/pages/library/borrow/create.blade.php`
- `resources/views/pages/library/elibrary/index.blade.php`
- `resources/views/pages/library/elibrary/create.blade.php`

### Transport Module

**Controllers:**
- ✅ `app/Http/Controllers/TransportController.php` - Created

**Livewire Components:**
- `app/Livewire/CreateBusForm.php`
- `app/Livewire/ListBuses.php`
- `app/Livewire/CreateBusRouteForm.php`
- `app/Livewire/ListBusRoutes.php`
- `app/Livewire/CreateRouteAssignmentForm.php`
- `app/Livewire/ListRouteAssignments.php`
- `app/Livewire/BusTrackingMap.php`

**Views:**
- `resources/views/pages/transport/buses/index.blade.php`
- `resources/views/pages/transport/buses/create.blade.php`
- `resources/views/pages/transport/routes/index.blade.php`
- `resources/views/pages/transport/routes/create.blade.php`
- `resources/views/pages/transport/assignments/index.blade.php`
- `resources/views/pages/transport/assignments/create.blade.php`
- `resources/views/pages/transport/tracking.blade.php`

---

## Routes Structure

Add to `routes/web.php`:

```php
// HR & Payroll Module
Route::prefix('hr')->name('hr.')->group(function () {
    Route::get('staff', [HRController::class, 'staffIndex'])->name('staff.index');
    Route::get('staff/create', [HRController::class, 'staffCreate'])->name('staff.create');
    
    Route::get('leaves', [HRController::class, 'leavesIndex'])->name('leaves.index');
    Route::get('leaves/create', [HRController::class, 'leavesCreate'])->name('leaves.create');
    
    Route::get('payroll', [HRController::class, 'payrollIndex'])->name('payroll.index');
    Route::get('payroll/create', [HRController::class, 'payrollCreate'])->name('payroll.create');
});

// Library Module
Route::prefix('library')->name('library.')->group(function () {
    Route::get('books', [LibraryController::class, 'booksIndex'])->name('books.index');
    Route::get('books/create', [LibraryController::class, 'booksCreate'])->name('books.create');
    
    Route::get('borrow', [LibraryController::class, 'borrowIndex'])->name('borrow.index');
    Route::get('borrow/create', [LibraryController::class, 'borrowCreate'])->name('borrow.create');
    
    Route::get('elibrary', [LibraryController::class, 'elibraryIndex'])->name('elibrary.index');
    Route::get('elibrary/create', [LibraryController::class, 'elibraryCreate'])->name('elibrary.create');
});

// Transport Module
Route::prefix('transport')->name('transport.')->group(function () {
    Route::get('buses', [TransportController::class, 'busesIndex'])->name('buses.index');
    Route::get('buses/create', [TransportController::class, 'busesCreate'])->name('buses.create');
    
    Route::get('routes', [TransportController::class, 'routesIndex'])->name('routes.index');
    Route::get('routes/create', [TransportController::class, 'routesCreate'])->name('routes.create');
    
    Route::get('assignments', [TransportController::class, 'assignmentsIndex'])->name('assignments.index');
    Route::get('assignments/create', [TransportController::class, 'assignmentsCreate'])->name('assignments.create');
    
    Route::get('tracking', [TransportController::class, 'tracking'])->name('tracking');
});
```

---

## Menu Structure

Add to `app/Livewire/Layouts/Menu.php`:

```php
['header' => 'Optional Modules', 'can' => 'read student'],

// HR & Payroll
[
    'type'    => 'menu-item',
    'text'    => 'HR & Payroll',
    'icon'    => 'fas fa-users-cog',
    'can'     => 'read student',
    'submenu' => [
        [
            'type'  => 'menu-item',
            'text'  => 'Staff Profiles',
            'route' => 'hr.staff.index',
            'can'   => 'read student',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'Leave Management',
            'route' => 'hr.leaves.index',
            'can'   => 'read student',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'Payroll',
            'route' => 'hr.payroll.index',
            'can'   => 'read student',
        ],
    ],
],

// Library
[
    'type'    => 'menu-item',
    'text'    => 'Library',
    'icon'    => 'fas fa-book',
    'can'     => 'read student',
    'submenu' => [
        [
            'type'  => 'menu-item',
            'text'  => 'Books Catalogue',
            'route' => 'library.books.index',
            'can'   => 'read student',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'Borrow/Return',
            'route' => 'library.borrow.index',
            'can'   => 'read student',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'E-Library',
            'route' => 'library.elibrary.index',
            'can'   => 'read student',
        ],
    ],
],

// Transport
[
    'type'    => 'menu-item',
    'text'    => 'Transport',
    'icon'    => 'fas fa-bus',
    'can'     => 'read student',
    'submenu' => [
        [
            'type'  => 'menu-item',
            'text'  => 'Buses',
            'route' => 'transport.buses.index',
            'can'   => 'read student',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'Routes',
            'route' => 'transport.routes.index',
            'can'   => 'read student',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'Assignments',
            'route' => 'transport.assignments.index',
            'can'   => 'read student',
        ],
        [
            'type'  => 'menu-item',
            'text'  => 'GPS Tracking',
            'route' => 'transport.tracking',
            'can'   => 'read student',
        ],
    ],
],
```

---

## Component Templates

### Example: List Component (Datatable Pattern)

```php
// app/Livewire/ListStaffProfiles.php
<?php
namespace App\Livewire;
use Livewire\Component;

class ListStaffProfiles extends Component
{
    public function mount()
    {
        $this->setErrorBag(session()->get('errors', new \Illuminate\Support\MessageBag())->getMessages());
    }

    public function render()
    {
        return view('livewire.list-staff-profiles');
    }
}
```

```blade
{{-- resources/views/livewire/list-staff-profiles.blade.php --}}
<div class="card">
    <div class="card-header flex justify-between items-center">
        <h2 class="card-title">Staff Profiles</h2>
        <a href="{{ route('hr.staff.create') }}" class="btn btn-primary">Add Staff</a>
    </div>
    <div class="card-body">
        <livewire:datatable 
            :model="App\Models\StaffProfile::class" 
            uniqueId="staff-profiles-table"
            :filters="[
                ['name' => 'inSchool'],
                ['name' => 'with', 'arguments' => ['user']],
                ['name' => 'orderBy', 'arguments' => ['created_at', 'desc']]
            ]"
            :columns="[
                ['property' => 'employee_id', 'name' => 'Employee ID'],
                ['property' => 'name', 'name' => 'Name', 'relation' => 'user'],
                ['property' => 'designation', 'name' => 'Designation'],
                ['property' => 'department', 'name' => 'Department'],
                ['property' => 'basic_salary', 'name' => 'Basic Salary'],
                ['property' => 'employment_type', 'name' => 'Type'],
            ]"
        />
    </div>
</div>
```

### Example: Create Form Component

```blade
{{-- resources/views/livewire/create-staff-profile-form.blade.php --}}
<div class="card">
    <div class="card-body">
        <h3 class="font-bold text-xl mb-4">Create Staff Profile</h3>

        <form wire:submit.prevent="save">
            <div class="grid md:grid-cols-2 gap-4">
                <div>
                    <label class="block font-semibold mb-2">Staff Member *</label>
                    <select wire:model="userId" class="w-full border rounded px-3 py-2" required>
                        <option value="">Select Staff</option>
                        @foreach($users as $user)
                            <option value="{{ $user->id }}">{{ $user->name }}</option>
                        @endforeach
                    </select>
                    @error('userId') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div>
                    <label class="block font-semibold mb-2">Employee ID *</label>
                    <input type="text" wire:model="employeeId" class="w-full border rounded px-3 py-2" required>
                    @error('employeeId') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div>
                    <label class="block font-semibold mb-2">Designation *</label>
                    <input type="text" wire:model="designation" class="w-full border rounded px-3 py-2" required>
                    @error('designation') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div>
                    <label class="block font-semibold mb-2">Department</label>
                    <input type="text" wire:model="department" class="w-full border rounded px-3 py-2">
                    @error('department') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div>
                    <label class="block font-semibold mb-2">Joining Date *</label>
                    <input type="date" wire:model="joiningDate" class="w-full border rounded px-3 py-2" required>
                    @error('joiningDate') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div>
                    <label class="block font-semibold mb-2">Employment Type *</label>
                    <select wire:model="employmentType" class="w-full border rounded px-3 py-2" required>
                        <option value="full-time">Full-time</option>
                        <option value="part-time">Part-time</option>
                        <option value="contract">Contract</option>
                    </select>
                    @error('employmentType') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div class="md:col-span-2">
                    <h4 class="font-semibold text-lg mb-2">Salary Details</h4>
                </div>

                <div>
                    <label class="block font-semibold mb-2">Basic Salary *</label>
                    <input type="number" wire:model="basicSalary" class="w-full border rounded px-3 py-2" min="0" step="0.01" required>
                    @error('basicSalary') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div>
                    <label class="block font-semibold mb-2">Housing Allowance</label>
                    <input type="number" wire:model="housingAllowance" class="w-full border rounded px-3 py-2" min="0" step="0.01">
                </div>

                <div>
                    <label class="block font-semibold mb-2">Transport Allowance</label>
                    <input type="number" wire:model="transportAllowance" class="w-full border rounded px-3 py-2" min="0" step="0.01">
                </div>

                <div>
                    <label class="block font-semibold mb-2">Other Allowance</label>
                    <input type="number" wire:model="otherAllowance" class="w-full border rounded px-3 py-2" min="0" step="0.01">
                </div>

                <div class="md:col-span-2">
                    <h4 class="font-semibold text-lg mb-2">Bank Details</h4>
                </div>

                <div>
                    <label class="block font-semibold mb-2">Bank Name</label>
                    <input type="text" wire:model="bankName" class="w-full border rounded px-3 py-2">
                    @error('bankName') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div>
                    <label class="block font-semibold mb-2">Account Number</label>
                    <input type="text" wire:model="accountNumber" class="w-full border rounded px-3 py-2">
                    @error('accountNumber') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>

                <div>
                    <label class="block font-semibold mb-2">Tax ID</label>
                    <input type="text" wire:model="taxId" class="w-full border rounded px-3 py-2">
                    @error('taxId') <span class="text-red-500 text-sm">{{ $message }}</span> @enderror
                </div>
            </div>

            <div class="mt-6 flex gap-2">
                <button type="submit" class="btn btn-primary">Create Profile</button>
                <a href="{{ route('hr.staff.index') }}" class="btn btn-secondary">Cancel</a>
            </div>
        </form>
    </div>
</div>
```

### Example: Page View

```blade
{{-- resources/views/pages/hr/staff/index.blade.php --}}
@extends('layouts.app', ['breadcrumbs' => [
    ['href'=> route('dashboard'), 'text'=> 'Dashboard'],
    ['href'=> route('hr.staff.index'), 'text'=> 'Staff Profiles', 'active'],
]])

@section('title', __('Staff Profiles'))
@section('page_heading', 'Staff Profiles')

@section('content')
    @livewire('list-staff-profiles')
@endsection
```

---

## Quick Implementation Steps

1. **Copy the routes** from this guide to `routes/web.php`
2. **Copy the menu items** to `app/Livewire/Layouts/Menu.php`
3. **Generate remaining Livewire components** using the patterns above
4. **Create page views** following the template structure
5. **Test each module** incrementally

---

## Status

✅ **Controllers Created** - HR, Library, Transport  
✅ **Example Livewire Component** - CreateStaffProfileForm  
✅ **Database & Models** - All ready  
📝 **Remaining** - Generate other Livewire components and views using templates above

---

**Next:** Use the component templates above to quickly generate the remaining UI files for all modules.
