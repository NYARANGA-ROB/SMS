# Kenya School System Localization

## Overview
This document outlines the localization features implemented to tailor the system for Kenyan schools.

## Implemented Changes

### 1. Currency & Financial
- **Currency**: Changed from generic to **Kenyan Shillings (KES)**
- **Salary Deductions**: Updated to match Kenyan tax system
  - PAYE (Pay As You Earn) - 10-30% progressive tax
  - NSSF (National Social Security Fund) - KES 200-2,160
  - NHIF (National Hospital Insurance Fund) - KES 150-1,700
- **School Fees**: Displayed in KES
- **Transport Fares**: Monthly bus fares in KES

### 2. Education System Structure
- **Terms**: Kenya uses 3 terms per year (Term 1, 2, 3)
- **Classes**: 
  - Primary: Standard 1-8 (Std 1-8)
  - Secondary: Form 1-4
- **Grading System**: A, B, C, D, E grading
- **National Exams**:
  - KCPE (Kenya Certificate of Primary Education)
  - KCSE (Kenya Certificate of Secondary Education)

### 3. Staff & HR
- **Designations**: 
  - Head Teacher / Principal
  - Deputy Head Teacher
  - Senior Teacher
  - Teacher (TSC employed)
  - BOM Teacher (Board of Management)
- **Leave Types**:
  - Annual Leave (28 days)
  - Sick Leave
  - Maternity Leave (90 days)
  - Paternity Leave (14 days)
  - Study Leave
  - Compassionate Leave

### 4. Transport & Routes
- **Common Routes**: Nairobi areas, Mombasa, Kisumu, etc.
- **Matatu Culture**: Acknowledge local transport context
- **GPS Tracking**: Essential for parent peace of mind

### 5. Library Categories
- **Kiswahili Literature**: Added as category
- **8-4-4 Curriculum**: Aligned with current system
- **CBC (Competency Based Curriculum)**: New curriculum support

### 6. Communication
- **SMS Integration**: Via Safaricom, Airtel Kenya
- **M-Pesa Integration**: Ready for fee payments
- **WhatsApp**: Popular communication channel

## Kenyan-Specific Features to Add

### Immediate Updates Needed:
1. ✅ Currency display (KES)
2. ✅ Kenyan tax deductions (PAYE, NSSF, NHIF)
3. ✅ Term-based academic calendar
4. ✅ Local leave types
5. ✅ Kenyan phone number format (+254)

### Future Enhancements:
- M-Pesa payment integration
- Safaricom Bulk SMS integration
- KCPE/KCSE exam management
- CBC assessment tracking
- TSC number tracking for teachers
- NEMIS integration (National Education Management Information System)

## Configuration

### Environment Variables
Add to `.env`:
```env
# Kenya Localization
APP_LOCALE=en_KE
APP_TIMEZONE=Africa/Nairobi
APP_CURRENCY=KES
APP_CURRENCY_SYMBOL=KSh

# SMS Gateway (Safaricom/Airtel)
SMS_PROVIDER=africastalking
SMS_USERNAME=your_username
SMS_API_KEY=your_api_key

# M-Pesa (Optional)
MPESA_CONSUMER_KEY=your_key
MPESA_CONSUMER_SECRET=your_secret
MPESA_SHORTCODE=your_shortcode
```

## Implementation Status

✅ **Completed:**
- Currency formatting (KES)
- Kenyan tax deductions in payroll
- Local leave types
- Phone number format
- Term-based structure

⏳ **Pending:**
- M-Pesa integration
- Safaricom SMS gateway
- NEMIS reporting
- CBC assessment module

---

**Last Updated:** 2025-09-30  
**Version:** 1.0
