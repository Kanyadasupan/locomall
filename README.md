# 🛍️ Locomall (โลคอลมอลล์) — Local Community Marketplace

> **แพลตฟอร์มตลาดชุมชนยุคใหม่ พร้อมระบบกระจายคำสั่งซื้อที่เป็นธรรม (Capacity-Aware Fair Order Allocation)**  
> พัฒนาด้วย **React Native (Expo SDK 57)**, **TypeScript**, **Supabase (PostgreSQL, Auth, Storage, Realtime, RLS & RPC)**

---

## 📖 สารบัญ (Table of Contents)
1. [เกี่ยวกับโครงการ (About the Project)](#-เกี่ยวกับโครงการ-about-the-project)
2. [แนวคิดหลักและโมเดลธุรกิจ (Core Business Concept)](#-แนวคิดหลักและโมเดลธุรกิจ-core-business-concept)
3. [กลไกการกระจายออเดอร์ (Capacity-Aware Order Allocation)](#-กลไกการกระจายออเดอร์-capacity-aware-order-allocation)
4. [บทบาทผู้ใช้งานในระบบ (System Actors & Roles)](#-บทบาทผู้ใช้งานในระบบ-system-actors--roles)
5. [ฟีเจอร์เด่นของระบบ (Key Features)](#-ฟีเจอร์เด่นของระบบ-key-features)
6. [สถาปัตยกรรมและเทคโนโลยีที่ใช้ (Tech Stack & Architecture)](#-สถาปัตยกรรมและเทคโนโลยีที่ใช้-tech-stack--architecture)
7. [โครงสร้างฐานข้อมูล (Database Schema)](#-โครงสร้างฐานข้อมูล-database-schema)
8. [โครงสร้างไดเรกทอรี (Project Directory Structure)](#-โครงสร้างไดเรกทอรี-project-directory-structure)
9. [การติดตั้งและเริ่มต้นใช้งาน (Getting Started)](#-การติดตั้งและเริ่มต้นใช้งาน-getting-started)
10. [ทีมงานผู้จัดทำ (Contributors)](#-ทีมงานผู้จัดทำ-contributors)

---

## 🎯 เกี่ยวกับโครงการ (About the Project)

**Locomall** เกิดขึ้นเพื่อแก้ปัญหาของแพลตฟอร์ม E-Commerce ทั่วไปที่ใช้โมเดลร้านค้าเดี่ยว (`Shop → Product → Order`) ซึ่งมักนำไปสู่ปัญหา:
- ผู้ค้ารายใหญ่หรือผู้มีทุนหนาตัดราคาสินค้า ทำให้เกษตรกรและผู้ค้ารายย่อยในชุมชนเสียเปรียบ
- ผู้ซื้อต้องสั่งซื้อสินค้าจากหลายร้านและเสียค่าส่งซ้ำซ้อน
- ผู้ค้ารายย่อยในท้องถิ่นขาดช่องทางการรวมกลุ่มเพื่อต่อรองหรือจัดจำหน่ายผลผลิตในปริมาณมาก

**Locomall จึงเปลี่ยนกระบวนทัศน์ใหม่** สู่:
> **"Temporary Community Market + Shared Product + Multiple Sellers + Automatic Order Allocation"**

โดยเปิดโอกาสให้ชุมชนสร้าง **"ตลาดเฉพาะกิจ/ตลาดชุมชน"** ขึ้นมา มีสินค้าส่วนกลาง (Market Product) และราคากลางที่ทุกคนยอมรับ จากนั้นผู้ขายในชุมชนสามารถนำสต็อกของตนเองมาร่วมลงขาย และเมื่อมีคำสั่งซื้อเข้ามา **ระบบจะกระจายยอดสั่งซื้อให้ผู้ขายแต่ละรายอย่างเป็นธรรมและทั่วถึงโดยอัตโนมัติ**

---

## 💡 แนวคิดหลักและโมเดลธุรกิจ (Core Business Concept)

### การเปรียบเทียบแนวคิดเดิม vs แนวคิด Locomall
| แนวคิดเดิม (Traditional E-Commerce) | แนวคิดของ Locomall (Community Marketplace) |
| :--- | :--- |
| ร้านค้าเดี่ยวแยกกันขาย (`Shop`) | ตลาดชุมชนร่วมกัน (`Market`) |
| เจ้าของร้านเป็นเจ้าของสินค้าคนเดียว | ตลาดกำหนดรายการสินค้าส่วนกลาง (`Market Product`) |
| สต็อกสินค้าผูกอยู่กับร้านค้าเดียว | ผู้ขายนำสต็อกมาร่วมลงขาย (`Seller Listing Quantity`) |
| ออเดอร์ส่งตรงไปยังร้านค้าที่ถูกเลือกรายเดียว | ออเดอร์ถูกกระจายไปยังผู้ขายหลายรายอัตโนมัติ (`Order Allocation`) |
| ผู้ค้ารายใหญ่ตัดราคา ผูกขาดการขาย | ราคากลางเป็นธรรม กระจายรายได้สู่ชุมชนเท่าเทียม |
| กระเป๋าเงินร้านค้าเดี่ยว (`Shop Wallet`) | บันทึกรายได้และธุรกรรมแยกตามผู้ขายจริง (`Seller Settlement`) |

---

## ⚖️ กลไกการกระจายออเดอร์ (Capacity-Aware Order Allocation)

หัวใจสำคัญของ Locomall คือ **อัลกอริทึมการกระจายออเดอร์โดยคำนึงถึงสต็อกสูงสุดของผู้ขาย (Capacity-Aware Equal Order Allocation)** เพื่อให้มั่นใจว่า:
1. คำสั่งซื้อถูกแบ่งให้ผู้ขายที่มีสินค้าอยู่อย่างเท่าเทียมที่สุด (Fair Share)
2. ไม่จัดสรรเกินจำนวนสต็อกที่ผู้ขายแต่ละรายมีอยู่จริง (Stock Cap Constraints)
3. หากผู้ขายรายใดสต็อกไม่พอ ส่วนที่เหลือจะถูกส่งต่อไปเฉลี่ยให้ผู้ขายรายอื่นที่มีสต็อกเหลืออยู่โดยอัตโนมัติ
4. รายได้คิดจาก: `fulfilled_quantity × unit_price` ตามยอดจัดส่งจริงของผู้ขายแต่ละคน

### 📊 ตัวอย่างการจัดสรรคำสั่งซื้อ

สมมติว่าในตลาดมีสินค้า **มะพร้าวน้ำหอม** ราคากลาง **20 บาท/ลูก**  
ผู้ขายที่เข้าร่วม:
- **Seller A**: มีสต็อกคงเหลือ 5 ลูก
- **Seller B**: มีสต็อกคงเหลือ 100 ลูก
- **Seller C**: มีสต็อกคงเหลือ 100 ลูก

เมื่อลูกค้าสั่งซื้อจำนวน **30 ลูก**:
- **รอบที่ 1**: ยอดสั่ง 30 ลูก แบ่ง 3 คน คนละ 10 ลูก
- **ตรวจสอบสต็อก**:
  - Seller A มีเพียง 5 ลูก ➔ จัดสรรให้ A ได้สูงสุด **5 ลูก** (ขาดอีก 5 ลูก)
- **รอบที่ 2 (Redistribution)**: ยอดที่เหลืออีก 5 ลูก ถูกนำมาเฉลี่ยให้ Seller B และ Seller C
  - Seller B ได้เพิ่ม 3 ลูก ➔ รวมเป็น **13 ลูก**
  - Seller C ได้เพิ่ม 2 ลูก ➔ รวมเป็น **12 ลูก**
- **สรุปผลการจัดสรร**:
  - **Seller A**: 5 ลูก (รายได้ $5 \times 20 = 100$ บาท)
  - **Seller B**: 13 ลูก (รายได้ $13 \times 20 = 260$ บาท)
  - **Seller C**: 12 ลูก (รายได้ $12 \times 20 = 240$ บาท)
  - **รวมส่งมอบ**: 30 ลูก ครบถ้วนตามยอดคำสั่งซื้อ

---

## 👥 บทบาทผู้ใช้งานในระบบ (System Actors & Roles)

ระบบรองรับ 4 บทบาทหลักพร้อมสิทธิ์การเข้าถึงอย่างรัดกุมผ่าน Supabase Row Level Security (RLS):

1. **ผู้ซื้อ (Buyer / User)**
   - ค้นหาตลาดและสินค้าชุมชนตามหมวดหมู่และพื้นที่
   - สั่งซื้อสินค้าในตลาดเดียวกัน ตรวจสอบยอดพรีวิวการกระจายสินค้า
   - ชำระเงินผ่านระบบกระเป๋าเงิน (Wallet)
   - ติดตามสถานะออเดอร์ทั้งระดับภาพรวมและระดับผู้ขายแต่ละราย (Allocations)
   - แชทสนทนากับผู้ขาย และให้คะแนนรีวิวหลังคำสั่งซื้อสำเร็จ

2. **ผู้ขายในชุมชน (Community Seller)**
   - สมัครเข้าร่วมเป็นสมาชิกในตลาดชุมชนในพื้นที่
   - นำสต็อกของตนเองเข้าร่วมลงขายในสินค้าส่วนกลาง (Seller Listing)
   - ตรวจสอบรายการจัดสรรสินค้าที่ตนเองได้รับ (Allocations)
   - จัดเตรียมสินค้าและอัปเดตสถานะการจัดส่ง
   - รับเงินเข้ากระเป๋าเงินเมื่อจัดส่งสำเร็จ พร้อมดูประวัติธุรกรรม

3. **ผู้สร้าง/เจ้าของตลาด (Market Owner)**
   - สร้างตลาดชุมชนชั่วคราว กำหนดพื้นที่และหมวดหมู่
   - สร้างและจัดการรายการสินค้าส่วนกลาง (Market Products) และกำหนดราคากลาง
   - ตรวจสอบและอนุมัติใบสมัครของผู้ขายในชุมชน
   - ติดตามภาพรวมยอดขายและการทำงานของตลาด

4. **ผู้ดูแลระบบ (Admin)**
   - อนุมัติการเปิดตลาดใหม่ของชุมชน
   - ตรวจสอบรายงาน (Reports) และระงับผู้ใช้งาน/ตลาดที่กระทำผิดเงื่อนไข
   - ดูแลความโปร่งใสของระบบธุรกรรมและการเงิน

---

## 🚀 ฟีเจอร์เด่นของระบบ (Key Features)

- 🔐 **ระบบความปลอดภัยและการยืนยันตัวตน (Authentication & Authorization)**:
  - สมัครสมาชิก, เข้าสู่ระบบ, กู้คืนรหัสผ่านด้วย Supabase Auth
  - จัดการข้อมูลโปรไฟล์และที่อยู่จัดส่ง (ตำบล, อำเภอ, จังหวัด)
- 🛒 **ระบบตลาดและสินค้าส่วนกลาง (Market & Shared Products)**:
  - หน้ารวมตลาดและรายละเอียดตลาด พร้อมระบบค้นหา
  - หน้ารายละเอียดสินค้าส่วนกลาง แสดงรายชื่อผู้ขายและสต็อกรวม
- 📦 **ระบบตะกร้าและการสั่งซื้อ (Smart Cart & Checkout)**:
  - สโคปตะกร้าสินค้าตามตลาด ป้องกันการสั่งซื้อข้ามตลาดที่อาจก่อให้เกิดปัญหาขนส่ง
  - คำนวณยอดจัดสรรล่วงหน้าแบบ Real-time
- ⚙️ **Order Allocation Engine**:
  - กระจายออเดอร์อัตโนมัติทั้งระดับ Client Engine และ Backend Database Function/RPC
  - การจองสต็อก (Reserved Stock) ป้องกันการขายเกินสต็อก (Overselling)
- 🚚 **ระบบติดตามคำสั่งซื้อแบบแยกส่วน (Granular Order & Allocation Tracking)**:
  - แยกสถานะคำสั่งซื้อระดับภาพรวมและสถานะการจัดส่งของผู้ขายแต่ละรายอย่างอิสระ
- 💰 **กระเป๋าเงินดิจิทัล (Internal Wallet & Settlement Ledger)**:
  - กระเป๋าเงินสำหรับผู้ซื้อและผู้ขาย
  - ระบบบันทึกบัญชีแยกประเภท (Ledger Transactions) ป้องกันข้อมูลการเงินผิดพลาด
- 💬 **ระบบสื่อสารและการแจ้งเตือน (Chat & Real-time Notifications)**:
  - แชทระหว่างผู้ซื้อและผู้ขายแบบเรียลไทม์
  - การแจ้งเตือนอัปเดตสถานะออเดอร์และการจัดสรร
- 🌐 **รองรับสองภาษา (Multi-language Support)**:
  - รองรับการสลับภาษาไทย (TH) และภาษาอังกฤษ (EN) ผ่าน `LanguageContext`

---

## 🛠️ สถาปัตยกรรมและเทคโนโลยีที่ใช้ (Tech Stack & Architecture)

```
┌────────────────────────────────────────────────────────┐
│                   Mobile Frontend                      │
│   React Native (0.86.3) • Expo SDK 57 • TypeScript    │
│   Expo Router (File-based) • React Navigation 7        │
│   Reanimated • Safe Area Context • Vector Icons        │
└──────────────────────────┬─────────────────────────────┘
                           │ HTTPS / WebSocket (Realtime)
┌──────────────────────────▼─────────────────────────────┐
│                 BaaS — Supabase Cloud                  │
│  ┌──────────────────────┬───────────────────────────┐  │
│  │ Supabase Auth        │ PostgreSQL Database       │  │
│  │ Session Management   │ Tables, Views, Triggers   │  │
│  ├──────────────────────┼───────────────────────────┤  │
│  │ Supabase Storage     │ Database Functions (RPC)  │  │
│  │ Image Buckets        │ Order Allocation & Ledger │  │
│  ├──────────────────────┼───────────────────────────┤  │
│  │ Supabase Realtime    │ Row Level Security (RLS)  │  │
│  │ Live Chat & Updates  │ Role-based Access Control │  │
│  └──────────────────────┴───────────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

- **Frontend Framework**: [React Native](https://reactnative.dev/) v0.86.3 / [Expo](https://expo.dev/) SDK 57
- **Routing**: [Expo Router](https://docs.expo.dev/router/introduction/) v57 (File-based Routing)
- **Language**: [TypeScript](https://www.typescriptlang.org/)
- **Backend & Database**: [Supabase](https://supabase.com/) (PostgreSQL 15+)
- **Security**: PostgreSQL Row Level Security (RLS), Stored Procedures & Transactions (RPC)
- **State & Context**: React Context API (`LanguageContext`, Custom Auth Hooks)

---

## 🗄️ โครงสร้างฐานข้อมูล (Database Schema)

ตารางหลักในระบบที่รองรับโมเดล Community Marketplace:
- `profiles`: ข้อมูลส่วนตัว บทบาท (`user`, `seller`, `market_owner`, `admin`) และยอดเงินในกระเป๋า
- `user_addresses`: ที่อยู่สำหรับจัดส่งสินค้า
- `markets`: ข้อมูลตลาดชุมชน เจ้าของตลาด พื้นที่ และสถานะการอนุมัติ
- `market_members`: สมาชิกผู้ขายในตลาดและสถานะการตรวจสอบ
- `market_products`: รายการสินค้าส่วนกลางประจำตลาด พร้อมราคากลาง
- `seller_listings`: สต็อก ราคา และสถานะการลงขายของผู้ขายแต่ละคน
- `orders`: ข้อมูลคำสั่งซื้อระดับภาพรวม เชื่อมโยงกับตลาด (`market_id`)
- `order_items`: รายการสินค้าที่สั่งซื้อ
- `order_allocations`: รายละเอียดการจัดสรรสินค้าให้กับผู้ขายแต่ละราย (`seller_id`, `allocated_quantity`, `status`)
- `chat_rooms` & `chat_messages`: ห้องสนทนาและข้อความแบบ Realtime
- `reviews`: รีวิวและคะแนนสินค้า/ผู้ขาย
- `wallet_transactions`, `seller_wallet_transactions`, `market_wallet_transactions`: บันทึกบัญชีธุรกรรมการเงิน

---

## 📁 โครงสร้างไดเรกทอรี (Project Directory Structure)

```text
locomall/
├── app/                      # เส้นทางหน้าจอตามโครงสร้าง Expo Router
│   ├── (auth)/               # ระบบยืนยันตัวตน (Sign In, Sign Up, OTP, Password)
│   ├── (tabs)/               # หน้าหลักแท็บล่าง (Home, Chat, Notification, Profile)
│   ├── market/               # ดูตลาด, สร้างตลาด และจัดการสินค้าในตลาด
│   ├── seller/               # แดชบอร์ดผู้ขาย, รายการลงขาย และรายการจัดสรรออเดอร์
│   ├── order/                # ตะกร้าสินค้า (Cart), ชำระเงิน (Checkout) และดูสถานะออเดอร์
│   ├── chat/                 # ห้องสนทนารายบุคคล
│   ├── profile/              # แก้ไขโปรไฟล์, ที่อยู่จัดส่ง, กระเป๋าเงิน และภาษา
│   ├── _layout.tsx           # Root Layout และ Navigation Provider
│   └── index.tsx             # Entry Redirect Screen
├── src/
│   ├── components/           # Reusable UI Components (Button, Header, ImagePlaceholder)
│   ├── constants/            # การตั้งค่า Theme, สี, และฟอนต์
│   ├── context/              # React Context (LanguageContext ฯลฯ)
│   ├── lib/
│   │   ├── allocation.ts     # กลไก Capacity-Aware Order Allocation Algorithm
│   │   └── supabase.ts       # Supabase Client Initialization
│   ├── mock/                 # Mock Data สำหรับทดสอบ UI
│   └── types/                # TypeScript Type Definitions
├── supabase/
│   └── schema.sql            # สคริปต์ DDL, Table Schemas, RLS Policies, Triggers & RPCs
├── assets/                   # รูปภาพ ไอคอน และฟอนต์
├── .env.example              # ตัวอย่างการตั้งค่า Environment Variables
├── app.json                  # การกำหนดค่า Expo Application Configuration
├── package.json              # รายการ Dependencies และ Scripts
└── tsconfig.json             # การตั้งค่า TypeScript Compiler
```

---

## ⚙️ การติดตั้งและเริ่มต้นใช้งาน (Getting Started)

### 1. ความต้องการของระบบ (Prerequisites)
- [Node.js](https://nodejs.org/) (เวอร์ชัน 18 ขึ้นไป แนะนำ LTS)
- [npm](https://www.npmjs.com/) หรือ [yarn](https://yarnpkg.com/)
- บัญชี [Supabase](https://supabase.com) สำหรับฐานข้อมูล
- สมาร์ตโฟนที่ติดตั้งแอป **Expo Go** หรือ Android Emulator / iOS Simulator

### 2. โคลนโปรเจกต์และติดตั้ง Dependencies
```bash
# Clone repository
git clone https://github.com/MIXThanakorn/locomall.git
cd locomall

# ติดตั้งแพ็กเกจ
npm install
```

### 3. ตั้งค่าตัวแปรสภาพแวดล้อม (Environment Variables)
คัดลอกไฟล์ `.env.example` ไปเป็น `.env`:
```bash
cp .env.example .env
```
เปิดไฟล์ `.env` และใส่ค่า API Keys จากโครงการ Supabase ของคุณ:
```env
EXPO_PUBLIC_SUPABASE_URL=https://your-project-ref.supabase.co
EXPO_PUBLIC_SUPABASE_ANON_KEY=your-anon-key-here
```

### 4. ติดตั้งฐานข้อมูล Supabase
1. ไปที่ Supabase Project Dashboard -> **SQL Editor**
2. นำเนื้อหาในไฟล์ `supabase/schema.sql` หรือ `supabase_setup.sql` ไปรันเพื่อสร้าง Tables, Enums, Triggers, RLS Policies และ Stored Procedures ทั้งหมด

### 5. เริ่มต้นรันแอปพลิเคชัน
```bash
# รันเซิร์ฟเวอร์ Expo Development
npm start

# หรือรันตรงไปยัง Platform ที่ต้องการ
npm run android   # สำหรับ Android Emulator หรืออุปกรณ์ Android ที่เชื่อมต่อ
npm run ios       # สำหรับ iOS Simulator (macOS เท่านั้น)
npm run web       # สำหรับเปิดทดสอบบน Web Browser
```

---

## 👩‍💻 ทีมงานผู้จัดทำ (Contributors)

โครงงานนี้พัฒนาโดยนักศึกษาสาขาวิชาเทคโนโลยีสารสนเทศ มหาวิทยาลัยเอเชียอาคเนย์ (SAU):
- **กัญญดา สุพรรณ (Kanyada Supan)** — [GitHub Profile](https://github.com/KanyadaSupan) | Email: `s6652410028@sau.ac.th`
- **ธนากร ทองไพรวัลย์ (Thanakorn Thongpraiwan / MIXThanakorn)** — [GitHub Profile](https://github.com/MIXThanakorn)

---

## 📄 ใบอนุญาต (License)
ลิขสิทธิ์โครงการเป็นของทีมผู้พัฒนา Locomall สงวนลิขสิทธิ์ตามกฎหมายสำหรับการศึกษาและวิจัย (Educational & Academic Project).
