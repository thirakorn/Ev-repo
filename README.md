# EV Charging Tracker

ระบบติดตามการชาร์จรถไฟฟ้า (EV) พร้อมวิเคราะห์สลิปอัตโนมัติด้วย AI

## Features

### AI Slip Analysis
- อัปโหลดรูปสลิปการชาร์จ (รองรับหลายรูป, drag & drop)
- AI วิเคราะห์ข้อมูลจากสลิปอัตโนมัติ: วันที่, สถานี, kWh, ราคา, ระยะเวลา
- รองรับการแปลงปี พ.ศ. → ค.ศ. อัตโนมัติ (เช่น 69 = พ.ศ. 2569 = ค.ศ. 2026)
- แก้ไขข้อมูลก่อนบันทึกได้

### Dashboard & Charts
- สรุปยอดรายสัปดาห์ / รายเดือน
- กราฟแนวโน้มรายเดือน (kWh, ราคา, จำนวนครั้ง)
- กราฟแนวโน้มราคาต่อ kWh
- กราฟวันที่ชาร์จบ่อย (Day of Week)
- กราฟสถานีที่ใช้บ่อย (จำนวนครั้ง / ราคา)
- กราฟเวลาเฉลี่ยต่อสถานี

### Data Management
- บันทึกข้อมูลการชาร์จลง Firestore (per-user)
- ดูประวัติย้อนหลัง + กรองรายเดือน
- ลบรายการได้
- สถิติรวม: จำนวนครั้ง, ราคารวม, kWh รวม, เฉลี่ย ฿/kWh

### Authentication
- Google Sign-In ผ่าน Firebase Auth
- ข้อมูลแยกตามผู้ใช้

## Tech Stack

- **Frontend:** HTML, CSS, Vanilla JavaScript (Single Page Application)
- **Backend:** Firebase (Firestore, Authentication, Remote Config, Hosting)
- **AI:** OpenRouter API (Gemini 2.5 Flash, GPT-4o, Claude 3.5 Sonnet, etc.)
- **Charts:** Chart.js v4.4.7

## Project Structure

```
Ev-repo/
├── public/
│   └── index.html          # Main SPA (all HTML, CSS, JS)
├── firebase.json            # Firebase Hosting config
├── firestore.rules          # Firestore security rules
├── firestore.indexes.json   # Firestore indexes
└── .firebaserc              # Firebase project config
```

## Setup

### Prerequisites
- Firebase project with Authentication (Google provider) and Firestore enabled
- OpenRouter API key

### Configuration

1. **Firebase Remote Config** — set the following parameters:
   - `openrouter_api_key` — your OpenRouter.ai API key
   - `ai_model` — default AI model (e.g. `google/gemini-2.5-flash`)

2. **Firestore Rules** — already configured for per-user access:
   ```
   users/{userId}/charges/{chargeId}
   → allow read, write: if auth.uid == userId
   ```

### Deploy

```bash
firebase deploy
```

## Firestore Data Structure

```
users/
  {uid}/
    charges/
      {docId}:
        date: "YYYY-MM-DD"
        station: "ชื่อสถานี"
        kwh: 0.00
        cost: 0.00
        duration: 0          # minutes
        note: "..."
        createdAt: Timestamp
```

## Buddhist Calendar Conversion

สลิปในไทยอาจแสดงปี พ.ศ. (พุทธศักราช):
- **พ.ศ. = ค.ศ. + 543**
- ปี 2 หลัก `69` = พ.ศ. 2569 = ค.ศ. 2026
- ปี 4 หลัก `2569` = ค.ศ. 2026

ระบบจะแปลงเป็นปี ค.ศ. อัตโนมัติทั้งใน AI prompt และ `convertBEtoCE()` safety net

## Supported AI Models

| Model | Provider |
|-------|----------|
| `google/gemini-2.5-flash` | Google (recommended) |
| `google/gemini-2.0-flash-001` | Google |
| `openai/gpt-4o-mini` | OpenAI |
| `openai/gpt-4o` | OpenAI |
| `anthropic/claude-3.5-sonnet` | Anthropic |
