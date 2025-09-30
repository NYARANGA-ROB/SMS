# Analytics Dashboard Feature

## Overview
The analytics dashboard combines **attendance**, **grades**, and **behaviour** data to identify at-risk students using a simple machine learning scoring model. It provides charts, a detailed table, and downloadable PDF reports for administrators.

## Features Implemented

### 1. Database Schema
- **`attendance_records`** table: Tracks daily attendance (present, absent, late, excused) with timestamps and late minutes
- **`behaviour_incidents`** table: Records behaviour incidents with severity levels (minor, moderate, major), points, and notes

### 2. Risk Scoring Model
**Location:** `app/Services/Analytics/RiskScoringService.php`

The service computes a risk score (0-100) for each student using weighted features:
- **Attendance rate** (40% weight): Lower attendance increases risk
- **Average grade** (40% weight): Grades below 70 increase risk
- **Behaviour points** (15% weight): Accumulated incident points increase risk
- **Late rate** (5% weight): Frequent lateness increases risk

**Intervention suggestions** are automatically generated based on thresholds:
- Attendance < 85% → "Call home and set attendance improvement plan"
- Avg grade < 60 → "Assign tutoring and weekly progress checks"
- Behaviour points ≥ 5 → "Counselor meeting and behaviour contract"
- Late rate > 20% → "Discuss punctuality and adjust schedule if needed"

### 3. Analytics Dashboard
**Route:** `/dashboard/analytics`  
**Livewire Component:** `AnalyticsDashboard`  
**View:** `resources/views/livewire/analytics-dashboard.blade.php`

**Features:**
- **Risk score histogram**: Bar chart showing distribution of risk scores across students
- **Feature radar chart**: Visual overview of average attendance, grades, behaviour, and lateness
- **At-risk students table**: Top 50 students sorted by risk score with detailed metrics and intervention suggestions
- **Download PDF button**: Exports the full report

### 4. PDF Report
**Route:** `/dashboard/analytics/download-at-risk`  
**Controller:** `AnalyticsController@downloadAtRisk`  
**View:** `resources/views/reports/at-risk-students.blade.php`

Generates a professional PDF report with:
- School name and generation timestamp
- Complete list of at-risk students (up to 200)
- All metrics and intervention suggestions

### 5. Attendance Management
**Routes:** `/dashboard/attendance` (list), `/dashboard/attendance/record` (record)  
**Livewire Components:** `RecordAttendance`, `ListAttendanceRecords`

**Features:**
- **Bulk attendance recording**: Select a section and date, mark all students at once
- **Status options**: Present, Absent, Late (with minutes), Excused
- **Auto-save**: Updates existing records if attendance already recorded for that date
- **Datatable view**: Search and browse all attendance records

### 6. Behaviour Incident Management
**Routes:** `/dashboard/behaviour-incidents` (list), `/dashboard/behaviour-incidents/create` (create)  
**Livewire Component:** `CreateBehaviourIncidentForm`, `ListBehaviourIncidents`

**Features:**
- **Severity levels**: Minor (1 pt), Moderate (3 pts), Major (5 pts) - auto-adjusts points
- **Categories**: Customizable incident categories (Disruption, Fighting, etc.)
- **Detailed notes**: Record full context of each incident
- **Datatable view**: Search by student name or category

### 7. Navigation
Added menu items in the sidebar under "Academics" section:
- **Attendance** (with submenu: View Records, Record Attendance)
- **Behaviour** (with submenu: View Incidents, Record Incident)
- **Analytics** (direct link to dashboard)

## Usage Instructions

### For Administrators & Teachers

#### Recording Attendance
1. **Navigate to Academics → Attendance → Record Attendance**
2. Select the **date** and **section/class**
3. Mark each student as:
   - **Present** - Student attended
   - **Absent** - Student did not attend
   - **Late** - Student arrived late (specify minutes)
   - **Excused** - Absence was excused
4. Click **Save Attendance**

#### Recording Behaviour Incidents
1. **Navigate to Academics → Behaviour → Record Incident**
2. Fill in the form:
   - Select the **student**
   - Choose **severity** (Minor/Moderate/Major) - points auto-adjust
   - Add **category** (e.g., Disruption, Fighting)
   - Adjust **points** if needed
   - Add detailed **notes**
3. Click **Save Incident**

#### Viewing Records
- **Attendance Records**: Academics → Attendance → View Records
- **Behaviour Incidents**: Academics → Behaviour → View Incidents

#### Analytics Dashboard
1. **Access the dashboard:**
   - Navigate to **Academics → Analytics** in the sidebar
   - Or visit `/dashboard/analytics` directly

2. **Review at-risk students:**
   - Check the risk score histogram to see overall distribution
   - Review the table for specific students requiring intervention
   - Read the suggested interventions for each student

3. **Download reports:**
   - Click **Download PDF Report** button
   - Share with counselors, teachers, or parents as needed

### For Developers

#### Adding Attendance Records
```php
use App\Models\AttendanceRecord;

AttendanceRecord::create([
    'user_id' => $student->id,
    'section_id' => $section->id,
    'school_id' => $school->id,
    'academic_year_id' => $academicYear->id,
    'semester_id' => $semester->id,
    'date' => now()->toDateString(),
    'status' => 'present', // or 'absent', 'late', 'excused'
    'minutes_late' => 15, // optional, for 'late' status
]);
```

#### Adding Behaviour Incidents
```php
use App\Models\BehaviourIncident;

BehaviourIncident::create([
    'user_id' => $student->id,
    'section_id' => $section->id,
    'school_id' => $school->id,
    'academic_year_id' => $academicYear->id,
    'semester_id' => $semester->id,
    'date' => now()->toDateString(),
    'severity' => 'moderate', // 'minor', 'moderate', or 'major'
    'category' => 'Disruption',
    'points' => 3,
    'notes' => 'Talking during class',
]);
```

#### Customizing the Risk Model
Edit `app/Services/Analytics/RiskScoringService.php`:
- Adjust weights in the `scoreStudents()` method
- Modify intervention thresholds in `suggestIntervention()`
- Add new features by querying additional data

## Technical Details

### Models
- `App\Models\AttendanceRecord`
- `App\Models\BehaviourIncident`

### Service
- `App\Services\Analytics\RiskScoringService`

### Controllers
- `App\Http\Controllers\AnalyticsController`

### Livewire Components
- `App\Livewire\AnalyticsDashboard`

### Routes
- `GET /dashboard/analytics` → Analytics dashboard
- `GET /dashboard/analytics/download-at-risk` → Download PDF report

### Permissions
Uses existing `read student` permission for access control.

## Charts & Visualization
Uses **Chart.js 4.4.3** (loaded via CDN) for:
- Bar chart (risk score histogram)
- Radar chart (feature overview)

## Next Steps (Optional Enhancements)

1. **Build attendance/behaviour management UIs** to populate data
2. **Add filters** for academic year, semester, class, or section
3. **Email alerts** for high-risk students
4. **Historical tracking** to monitor improvement over time
5. **More sophisticated ML models** (e.g., using PHP-ML library)
6. **Export to Excel** in addition to PDF
7. **Parent portal integration** to share student progress

## Testing

To test with sample data, you can use Laravel Tinker or seeders to populate `attendance_records` and `behaviour_incidents` tables. The dashboard will automatically compute risk scores and display results.

---

**Created:** 2025-09-29  
**Version:** 1.0
