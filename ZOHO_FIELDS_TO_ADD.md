# Zoho CRM Field Names to Add

## Instructions
Add these fields to the **Lead** module in Zoho CRM. All fields should be created as **Custom Fields**.

---

## Field List (15 Fields Total)

### 1. **Payment_Failed**
- **Field Type:** Single Line
- **Field Label:** Payment Failed
- **API Name:** `Payment_Failed`
- **Description:** Tracks when a payment fails
- **Sample Values:** "Payment Failed"

### 2. **Renewal_Failed**
- **Field Type:** Single Line
- **Field Label:** Renewal Failed
- **API Name:** `Renewal_Failed`
- **Description:** Tracks when a renewal payment fails
- **Sample Values:** "Renewal Failed"

### 3. **Diabetes_Type**
- **Field Type:** Single Line (or Picklist)
- **Field Label:** Diabetes Type
- **API Name:** `Diabetes_Type`
- **Description:** Type of diabetes user has
- **Sample Values:** "Type 1", "Type 2", "Prediabetes", "Gestational"

### 4. **FreeTrial_Mandate_Setup**
- **Field Type:** Single Line
- **Field Label:** FreeTrial Mandate Setup
- **API Name:** `FreeTrial_Mandate_Setup`
- **Description:** Tracks when user starts free trial
- **Sample Values:** "Trial Started"

### 5. **Trial_Start_Date**
- **Field Type:** Date/Time
- **Field Label:** Trial Start Date
- **API Name:** `Trial_Start_Date`
- **Description:** Date when trial started

### 6. **Trial_End_Date**
- **Field Type:** Date/Time
- **Field Label:** Trial End Date
- **API Name:** `Trial_End_Date`
- **Description:** Date when trial ends

### 7. **Trial_Cancel_Requested**
- **Field Type:** Single Line (or Checkbox)
- **Field Label:** Trial Cancel Requested
- **API Name:** `Trial_Cancel_Requested`
- **Description:** Whether user requested cancellation
- **Sample Values:** "Yes", "No"

### 8. **Cancellation_Reason**
- **Field Type:** Multi Line (or Picklist)
- **Field Label:** Cancellation Reason
- **API Name:** `Cancellation_Reason`
- **Description:** Reason for subscription cancellation
- **Sample Values:** "user_requested", "too_expensive", "not_useful", etc.

### 9. **Cancellation_Effective_Date**
- **Field Type:** Date/Time
- **Field Label:** Cancellation Effective Date
- **API Name:** `Cancellation_Effective_Date`
- **Description:** Date when cancellation takes effect

### 10. **Cancellation_Date**
- **Field Type:** Date/Time
- **Field Label:** Cancellation Date
- **API Name:** `Cancellation_Date`
- **Description:** Actual date of cancellation

### 11. **Renewal**
- **Field Type:** Single Line
- **Field Label:** Renewal
- **API Name:** `Renewal`
- **Description:** Tracks successful renewal
- **Sample Values:** "Renewal Success"

### 12. **Last_Renewal_Date**
- **Field Type:** Date/Time
- **Field Label:** Last Renewal Date
- **API Name:** `Last_Renewal_Date`
- **Description:** Date of last successful renewal

### 13. **Refunded_Cancelled**
- **Field Type:** Single Line (or Picklist)
- **Field Label:** Refunded/Cancelled
- **API Name:** `Refunded_Cancelled`
- **Description:** Tracks refund or cancellation status
- **Sample Values:** "Refunded", "Cancelled"

### 14. **Refund_Amount**
- **Field Type:** Currency
- **Field Label:** Refund Amount
- **API Name:** `Refund_Amount`
- **Description:** Amount refunded to user

### 15. **Refund_Date**
- **Field Type:** Date/Time
- **Field Label:** Refund Date
- **API Name:** `Refund_Date`
- **Description:** Date when refund was processed

---

## Already Existing Fields (Should Already Be in Zoho)

These fields are already being sent by the backend, make sure they exist:

- `Deal_Won` - Tracks successful payment
- `Subscription_Status` - Current subscription status (ACTIVE, TRIAL, CANCELLED, etc.)
- `Subscription_Plan` - Plan name
- `Subscription_Duration` - Plan interval/duration
- `Subscription_Amount` - Subscription amount in rupees
- `Purchase_Date` - Date of purchase
- `Next_Renewal_Date` - Next renewal date
- `App_Onboarding_Completed` - Onboarding completion status
- `App_Profile_Created` - Profile creation status
- `Last_Name` - User's last name
- `Email` - User's email
- `Name` - User's name
- `Gender` - User's gender
- `Age` - User's age

---

## Summary

**Total New Fields to Add:** 15
**Total Fields Already Existing:** 14+ (from previous implementation)

After adding these fields in Zoho CRM, the backend will automatically start populating them when the corresponding events occur.

---

## Testing After Field Creation

1. Create a test lead in Zoho
2. Trigger events from the backend (payment, onboarding, etc.)
3. Verify fields are being populated correctly
4. Check notification service logs for any errors

---

## Notes

- All field API names must match exactly as shown (case-sensitive)
- Make sure fields are added to the **Lead** module, not Contact or Account
- Currency fields should be in INR
- Date/Time fields should accept ISO 8601 format
