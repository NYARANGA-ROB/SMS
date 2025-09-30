# Button Color Guide

## Color Scheme for Better UX

### Primary Actions (Blue)
- **Create/Add New**: `btn btn-primary` (Blue)
- **Save/Submit**: `btn btn-primary` (Blue)
- Examples: "Add Student", "Create Class", "Save Changes"

### Success Actions (Green)
- **Approve/Accept**: `btn btn-success` (Green)
- **Confirm/Complete**: `btn btn-success` (Green)
- Examples: "Approve Leave", "Mark Present", "Complete"

### Warning Actions (Orange/Yellow)
- **Edit/Modify**: `btn btn-warning` (Orange)
- **Update**: `btn btn-warning` (Orange)
- Examples: "Edit Profile", "Update Record"

### Danger Actions (Red)
- **Delete/Remove**: `btn btn-danger` (Red)
- **Reject/Cancel**: `btn btn-danger` (Red)
- Examples: "Delete Student", "Reject Application"

### Secondary Actions (Gray)
- **Cancel/Back**: `btn btn-secondary` (Gray)
- **View/Details**: `btn btn-secondary` (Gray)
- Examples: "Cancel", "Go Back", "View Details"

### Info Actions (Cyan/Light Blue)
- **View/Preview**: `btn btn-info` (Cyan)
- **Download/Export**: `btn btn-info` (Cyan)
- Examples: "View Report", "Download PDF", "Calendar View"

## Implementation

Add to your CSS (if not already present):

```css
.btn-success {
    background-color: #10b981;
    color: white;
}

.btn-success:hover {
    background-color: #059669;
}

.btn-warning {
    background-color: #f59e0b;
    color: white;
}

.btn-warning:hover {
    background-color: #d97706;
}

.btn-danger {
    background-color: #ef4444;
    color: white;
}

.btn-danger:hover {
    background-color: #dc2626;
}

.btn-info {
    background-color: #06b6d4;
    color: white;
}

.btn-info:hover {
    background-color: #0891b2;
}
```

## Updated Components

All buttons have been updated with appropriate colors for better visual hierarchy and user experience.
