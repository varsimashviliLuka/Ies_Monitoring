# ავთენტიფიკაციის არქიტექტურა (Authentication Design)

## 1. მიზანი

ავთენტიფიკაციის მოდულის მიზანია მომხმარებლების უსაფრთხო იდენტიფიკაცია, სისტემაში წვდომის კონტროლი და დაცული რესურსების გამოყენების უზრუნველყოფა.

სისტემა იყენებს JWT (JSON Web Token) ტექნოლოგიას, რომელიც უზრუნველყოფს Stateless Authentication მექანიზმს და საშუალებას იძლევა მობილურმა აპლიკაციებმა უსაფრთხოდ დაუკავშირდნენ Backend API-ს.

---

# 2. ძირითადი ფუნქციები

- მომხმარებლის რეგისტრაცია;
- ავტორიზაცია (Login);
- Access Token-ის განახლება (Refresh Token);
- სისტემიდან გამოსვლა (Logout);
- პაროლის შეცვლა;
- პაროლის აღდგენა ელ-ფოსტის მეშვეობით;
- მომხმარებლის საკუთარი პროფილის მართვა.

---

# 3. გამოყენებული ტექნოლოგიები

| კომპონენტი | ტექნოლოგია |
|-----------|------------|
| Authentication | JWT (JSON Web Token) |
| Password Hashing | bcrypt |
| Secure Communication | HTTPS/SSL |
| Token Storage | MySQL |
| Email Service | SMTP Service |
| Mobile Clients | React Native (Android & iOS) |

---

# 4. ავთენტიფიკაციის პროცესი

```text
მომხმარებელი
      │
      ▼
მობილური აპლიკაცია
      │
      ▼
POST /api/auth/login
      │
      ▼
Backend API
      │
      ├── მომხმარებლის გადამოწმება
      ├── პაროლის გადამოწმება
      ├── Access Token-ის გენერაცია
      └── Refresh Token-ის გენერაცია
      │
      ▼
MySQL მონაცემთა ბაზა
```

---

# 5. Token-ების მართვა

## Access Token

დანიშნულება:
- API მოთხოვნების ავტორიზაცია.

მოქმედების ვადა:
- 1 საათი.

მაგალითი:

```json
{
  "sub": "user_uuid",
  "email": "user@example.com",
  "type": "access"
}
```

---

## Refresh Token

დანიშნულება:
- Access Token-ის განახლება ხელახალი ავტორიზაციის გარეშე.

მოქმედების ვადა:
- 30 დღე.

მაგალითი:

```json
{
  "sub": "user_uuid",
  "type": "refresh"
}
```

---

# 6. API Endpoint-ები

## ავტორიზაცია

| მეთოდი | Endpoint | აღწერა |
|--------|-----------|---------|
| POST | /api/auth/register | ახალი მომხმარებლის რეგისტრაცია |
| POST | /api/auth/login | ავტორიზაცია |
| POST | /api/auth/refresh | Access Token-ის განახლება |
| POST | /api/auth/logout | სისტემიდან გამოსვლა |

---

## პაროლის მართვა

| მეთოდი | Endpoint | აღწერა |
|--------|-----------|---------|
| PUT | /api/change_password | ავტორიზებული მომხმარებლის პაროლის შეცვლა |
| POST | /api/request_reset_password | პაროლის აღდგენის მოთხოვნა |
| PUT | /api/reset_password | პაროლის განახლება Reset Token-ის გამოყენებით |

---

## მომხმარებლის პროფილი

| მეთოდი | Endpoint | აღწერა |
|--------|-----------|---------|
| GET | /api/user | საკუთარი პროფილის მიღება |
| PUT | /api/user | საკუთარი პროფილის განახლება |

---

# 7. Login Flow

```text
მომხმარებელი
      │
      ├── Email
      └── Password
              │
              ▼
      POST /api/auth/login
              │
              ▼
         Backend API
              │
              ├── მომხმარებლის მოძიება
              ├── პაროლის გადამოწმება
              └── Token-ების გენერაცია
              │
              ▼
      Access Token + Refresh Token
              │
              ▼
      მობილური აპლიკაცია
```

---

# 8. Refresh Token Flow

```text
Access Token Expired
           │
           ▼
POST /api/auth/refresh
           │
           ▼
Refresh Token Validation
           │
           ▼
New Access Token
```

---

# 9. პაროლის აღდგენის პროცესი

```text
მომხმარებელი
      │
      ▼
POST /api/request_reset_password
      │
      ▼
ელ-ფოსტაზე იგზავნება Reset Link
      │
      ▼
PUT /api/reset_password
      │
      ▼
ახალი პაროლის შენახვა
```

---

# 10. უსაფრთხოების მექანიზმები

- JWT Authentication;
- bcrypt Password Hashing;
- HTTPS/SSL Encryption;
- Token Revocation;
- Refresh Token Rotation;
- Rate Limiting;
- Secure HTTP Headers;
- CSRF დაცვის მექანიზმები ადმინისტრაციული ინტერფეისისთვის;
- აუდიტის ლოგირება (Audit Logging).

---

# 11. შეცდომების კოდები

| კოდი | აღწერა |
|------|---------|
| 400 | არასწორი მოთხოვნა |
| 401 | ავტორიზაცია ვერ შესრულდა |
| 403 | წვდომა აკრძალულია |
| 404 | რესურსი ვერ მოიძებნა |
| 409 | კონფლიქტი |
| 429 | მოთხოვნების რაოდენობა გადაჭარბებულია |
| 500 | სერვერის შიდა შეცდომა |

---

# 12. უსაფრთხოების მოთხოვნები

- ყველა API Endpoint ხელმისაწვდომია მხოლოდ HTTPS-ის მეშვეობით;
- პაროლები არასოდეს ინახება ღია ტექსტით;
- JWT Secret ინახება გარემოს ცვლადებში (Environment Variables);
- Refresh Token-ები ინახება დაშიფრული სახით;
- სისტემას აქვს წარუმატებელი ავტორიზაციის მცდელობების ლიმიტი.

---

# 13. მომავალი გაფართოებები

- Google Login;
- Apple Sign-In;
- Multi-Factor Authentication (MFA);
- Single Sign-On (SSO);
- Biometric Authentication;
- OAuth2 / OpenID Connect მხარდაჭერა.