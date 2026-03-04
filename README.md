# EV Charger Tracking

แอปพลิเคชันติดตามค่าใช้จ่ายการชาร์จรถยนต์ไฟฟ้า (EV) สำหรับผู้ใช้งานในประเทศไทย รองรับการวิเคราะห์สลิปด้วย AI และแสดงสถิติการชาร์จในรูปแบบกราฟ

## Features

### AI Slip Analysis
- อัปโหลดรูปสลิปชาร์จ (รองรับหลายรูปพร้อมกัน)
- AI วิเคราะห์และดึงข้อมูลจากสลิปอัตโนมัติ: วันที่, สถานีชาร์จ, kWh, ค่าใช้จ่าย, ระยะเวลา
- รองรับรูปแบบวันที่หลากหลาย: `DD/MM/YYYY`, `YY/MM/DD`, ปี พ.ศ. (`3/3/2569`, `2569/3/3`)
- กรอกข้อมูลเองได้ถ้าไม่ต้องการใช้ AI

### Dashboard & Analytics
- สรุปสถิติ: จำนวนครั้ง, ค่าใช้จ่ายรวม, kWh รวม, ค่าเฉลี่ยต่อ kWh
- สรุปรายสัปดาห์และรายเดือน
- กราฟ 6 รูปแบบ:
  - แนวโน้มรายเดือน (kWh / ค่าใช้จ่าย / ความถี่)
  - ราคาต่อ kWh ตามเวลา
  - การกระจายตามวันในสัปดาห์
  - วิเคราะห์สถานีชาร์จ (ตามจำนวนครั้ง / ตามค่าใช้จ่าย)
  - ระยะเวลาเฉลี่ยตามสถานี

### Data Management
- ประวัติการชาร์จพร้อมกรองตามเดือน
- ลบรายการได้
- ข้อมูลแยกตาม user (Firebase Auth)

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | HTML / CSS / JavaScript (Single Page Application) |
| Auth | Firebase Authentication (Google Sign-In) |
| Database | Cloud Firestore |
| Config | Firebase Remote Config |
| Hosting | Firebase Hosting |
| AI/OCR | OpenRouter API (Gemini 2.5 Flash, GPT-4o, Claude 3.5 Sonnet) |
| Charts | Chart.js 4.4.7 |

## Project Structure

```
EV-Charger-Tracking/
├── public/
│   └── index.html          # Single-page application (all HTML/CSS/JS)
├── firebase.json           # Firebase hosting & Firestore config
├── firestore.rules         # Database security rules
├── firestore.indexes.json  # Firestore indexes
└── .firebaserc             # Firebase project mapping
```

## Setup

### Prerequisites
- Firebase CLI (`npm install -g firebase-tools`)
- Firebase project with Authentication, Firestore, Remote Config, and Hosting enabled

### Firebase Remote Config
Set the following parameters in Firebase Console > Remote Config:

| Parameter | Description |
|-----------|-------------|
| `openrouter_api_key` | API key from [OpenRouter](https://openrouter.ai/) |
| `ai_model` | AI model ID (default: `google/gemini-2.5-flash`) |

### Deploy

```bash
firebase login
firebase deploy
```

No build step required — the app is a single HTML file served as static content.

## Firestore Structure

```
users/
  {userId}/
    charges/
      {chargeId}/
        date: "YYYY-MM-DD"
        station: "string"
        kwh: number
        cost: number
        duration_minutes: number
        note: "string"
        createdAt: timestamp
```

### Security Rules
- Authenticated users can only read/write their own data
- Path: `/users/{userId}/charges/{chargeId}` — requires `request.auth.uid == userId`

## AI Models

Configurable via Settings. Supported models:

| Model | Provider |
|-------|----------|
| Gemini 2.5 Flash (default) | Google |
| Gemini 2.0 Flash | Google |
| GPT-4o Mini | OpenAI |
| GPT-4o | OpenAI |
| Claude 3.5 Sonnet | Anthropic |

## Date Format Handling

Thai EV charging slips use various date formats. The app handles all of them:

| Format on Slip | Example | Interpretation |
|---------------|---------|----------------|
| DD/MM/YYYY | 3/3/2026 | March 3, 2026 |
| DD/MM/YY | 3/3/26 | March 3, 2026 |
| YY/MM/DD | 26/3/3 | March 3, 2026 |
| YYYY/MM/DD | 2026/3/3 | March 3, 2026 |
| DD/MM/YYYY (พ.ศ.) | 3/3/2569 | March 3, 2026 |
| YYYY/MM/DD (พ.ศ.) | 2569/3/3 | March 3, 2026 |

Buddhist Era (พ.ศ.) years > 2400 are automatically converted to CE (ค.ศ.) by subtracting 543.

## License

Private project.
