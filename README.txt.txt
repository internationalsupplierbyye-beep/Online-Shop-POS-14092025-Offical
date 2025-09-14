POS Cloud – v3 (Roles via Firestore + Reports + Email Login)
===========================================================

မည်သို့သုံးရမည်

1) Firebase Authentication
   - Sign-in method → Email/Password → Enable
   - Users → Admin / Staff အကောင့်များ Create လုပ်

2) Firestore → Roles collection
   - Collection: roles
   - Document ID = User UID (Authentication → Users → UID ကို copy)
   - Field: role = "admin"  သို့မဟုတ်  "staff"

3) Firestore Rules (Copy/ Paste → Publish)
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Orders
    match /orders/{docId} {
      allow read, write: if isAdmin();     // admin: full
      allow create: if isStaff();          // staff: create only
    }
    // Roles (Admin only)
    match /roles/{userId} {
      allow read, write: if isAdmin();
    }
  }
}
function isAdmin() {
  return request.auth != null
    && get(/databases/$(database)/documents/roles/$(request.auth.uid)).data.role == "admin";
}
function isStaff() {
  return request.auth != null
    && get(/databases/$(database)/documents/roles/$(request.auth.uid)).data.role == "staff";
}

4) အပ်ကို ဖွင့်ခြင်း
   - index.html ကို file:// မဟုတ်ဘဲ local server နဲ့ဖွင့်ရန်
     * VS Code → Live Server
     * အခြားနည်း → terminal:  python -m http.server 8080
       ပြီးရင် http://localhost:8080/index.html သို့ဝင်ပါ

5) အသုံးပြုခြင်း
   - Login (Email/Password)
   - Settings (Admin only) → Delivery Companies / Staff IDs (one per line) → Save
   - Order Form ဖြည့်ပြီး → Save to Cloud
   - Reports (Admin only) → Date Range + Filters → Run → Export CSV

မှတ်ချက်များ
- Settings များကို browser localStorage ထဲသိမ်းထားသည်
- Orders ကို 'orders' collection ထဲသိမ်းသည်
- Order Number ကိုသိမ်းသလို Reports မှာ Filter လုပ်လို့ရသည်
- Admin/Staff အလိုက် Button/Tab များကို UI တစ်ဖက်တည်းက Hide/Disable လုပ်ထားသည်
