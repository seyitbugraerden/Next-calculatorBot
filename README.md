<div align="center">

# 🎓 Esas Puan Hesaplama

### LGS, TYT, AYT and YDT score calculation & exam countdown platform

A modern education utility built with **Next.js 15, React 19 and TypeScript**, providing exam score calculations, estimated ranking results and live countdown tools for major Turkish national exams.

<br />

![Next.js](https://img.shields.io/badge/Next.js-15.3-000000?style=for-the-badge&logo=nextdotjs&logoColor=white)
![React](https://img.shields.io/badge/React-19-61DAFB?style=for-the-badge&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript&logoColor=white)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-4-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white)

</div>

---

## About the Project

**Esas Puan Hesaplama** is an education-focused calculation platform developed with **Next.js 15**, **React 19**, and **TypeScript**.

The application provides tools for:

- LGS score calculation
- TYT score calculation
- AYT score calculation
- YDT score calculation
- Estimated rankings
- Placement scores
- Diploma score / OBP calculations
- Exam countdown timers

The project uses mathematical formulas and **cubic spline interpolation** to estimate rankings from calculated scores.

---

## Main Features

### Score Calculators

The platform currently provides:

```text id="cb001"
LGS Puan Hesaplama
TYT Puan Hesaplama
AYT Puan Hesaplama
YDT Puan Hesaplama
```

### Exam Countdown Tools

```text id="cb002"
LGS Kaç Gün Kaldı?
TYT Kaç Gün Kaldı?
AYT Kaç Gün Kaldı?
YDT Kaç Gün Kaldı?
```

Countdown pages display:

- Days
- Hours
- Minutes
- Seconds

with real-time updates.

---

## Application Routes

```text id="cb003"
/
│
├── /lgs-puan-hesaplama
├── /tyt-puan-hesaplama
├── /ayt-puan-hesaplama
├── /ydt-puan-hesaplama
│
├── /lgs-kac-gun-kaldi
├── /tyt-kac-gun-kaldi
├── /ayt-kac-gun-kaldi
└── /ydt-kac-gun-kaldi
```

---

## Tech Stack

| Technology | Purpose |
| --- | --- |
| **Next.js 15.3** | Application framework |
| **React 19** | User interface |
| **TypeScript 5** | Type safety |
| **Tailwind CSS 4** | Styling |
| **React Hook Form** | Calculator form state |
| **cubic-spline** | Ranking interpolation |
| **Custom CubicSpline** | Internal interpolation engine |
| **Radix UI** | Accessible UI primitives |
| **Lucide React** | Icons |
| **React Icons** | Additional iconography |
| **Swiper** | Slider dependency |
| **jsPDF** | PDF generation dependency |
| **jsPDF AutoTable** | PDF table generation dependency |

---

## Homepage

The homepage groups the tools into two main areas.

### Score Calculation

```text id="cb004"
LGS Puan Hesaplayıcı
TYT Puan Hesaplayıcı
AYT Puan Hesaplayıcı
YDT Puan Hesaplayıcı
```

### Exam Countdown

```text id="cb005"
LGS Kaç Gün Kaldı?
TYT Kaç Gün Kaldı?
AYT Kaç Gün Kaldı?
YDT Kaç Gün Kaldı?
```

Each card links directly to the corresponding tool.

---

## Calculation Architecture

The main calculation logic is centralized in:

```text id="cb006"
app/action.ts
```

This module contains:

- Net calculations
- TYT score calculations
- AYT score calculations
- YDT score calculations
- Placement scores
- Ranking estimations
- Score limits
- OBP contributions
- Cubic spline lookups

---

## Net Calculation

For YKS-based calculations, net values use:

```ts id="cb007"
dogru - yanlis / 4
```

through:

```ts id="cb008"
export const calculateNet = (
  dogru: number,
  yanlis: number
) => Math.max(0, dogru - yanlis / 4);
```

This prevents calculated nets from falling below zero.

---

## LGS Net Calculation

The LGS calculator uses:

```ts id="cb009"
dogru - yanlis / 3
```

to calculate subject-level net values.

---

## TYT Calculation

The calibrated TYT formula currently uses:

```ts id="cb010"
144.98
+ 2.908 × Türkçe
+ 2.937 × Sosyal
+ 2.925 × Matematik
+ 3.148 × Fen
```

The calculated result is then used in placement and ranking calculations.

---

## AYT Score Types

The AYT calculation architecture supports multiple score categories:

```text id="cb011"
SAY
EA
SÖZ
```

alongside TYT values.

---

## Numerical Score

The SAY calculation uses:

```text id="cb012"
TYT
AYT Mathematics
Physics
Chemistry
Biology
```

to produce:

- Raw score
- Placement score
- Estimated raw ranking
- Estimated placement ranking

---

## Equal Weight Score

The EA calculation uses:

```text id="cb013"
TYT
AYT Mathematics
Turkish Literature
History-1
Geography-1
```

---

## Verbal Score

The SÖZ calculation uses:

```text id="cb014"
TYT
Literature
History-1
Geography-1
History-2
Geography-2
Philosophy
Religion
```

---

## YDT Calculation

The foreign-language score uses:

```text id="cb015"
TYT Score
Foreign Language Net
```

The current formula is based on:

```ts id="cb016"
tytHam * 0.51415
+ dilNet * 2.60942
+ 36.05689
```

---

## Placement Score

Diploma score is added to the calculated raw score.

Current logic:

```text id="cb017"
Placement Score
=
Raw Score
+
Diploma Grade × 0.6
```

The diploma grade is capped at:

```text id="cb018"
100
```

and placement scores are capped at:

```text id="cb019"
560
```

where relevant.

---

## Ranking Estimation

One of the more technical parts of the project is the ranking estimation system.

Instead of using a simple linear formula, the application uses:

```text id="cb020"
Cubic Spline Interpolation
```

to estimate rankings between known score/ranking data points.

---

## Custom Cubic Spline Engine

The repository contains its own spline implementation:

```text id="cb021"
lib/spline.ts
```

Example usage:

```ts id="cb022"
const spline = new CubicSpline(
  puanlar,
  siralamalar
);

spline.at(score);
```

---

## Ranking Data

Score and ranking datasets are stored in:

```text id="cb023"
lib/spline-mock.ts
```

Separate datasets exist for:

```text id="cb024"
TYT
YDT
SAY
EA
SÖZ
```

including both raw and placement ranking curves.

---

## Ranking Flow

```text id="cb025"
User Inputs
   │
   ▼
Net Calculation
   │
   ▼
Score Formula
   │
   ▼
Calculated Score
   │
   ▼
Cubic Spline
   │
   ▼
Estimated Ranking
```

---

## LGS Ranking Estimation

LGS uses its own score/ranking dataset.

The calculator interpolates between known:

```text id="cb026"
Score → Ranking
```

pairs.

It additionally calculates an estimated:

```text id="cb027"
Percentile
```

from the estimated ranking.

---

## Form Validation

Calculators use:

```text id="cb028"
react-hook-form
```

for input management.

The forms dynamically prevent:

```text id="cb029"
Correct + Wrong > Total Questions
```

for each subject.

---

## Dynamic Input Limits

For each subject:

```text id="cb030"
Max Correct
=
Total Questions - Wrong
```

and:

```text id="cb031"
Max Wrong
=
Total Questions - Correct
```

This improves input consistency before calculation.

---

## Empty Input Protection

Calculators detect when no exam values are entered.

Example feedback:

```text id="cb032"
Herhangi bir değer girilmedi.
```

or:

```text id="cb033"
Lütfen en az bir dersten doğru veya yanlış değeri giriniz.
```

---

## Exam Countdown

Countdown pages use:

```text id="cb034"
setInterval()
```

to update the remaining exam time every second.

Example structure:

```text id="cb035"
Exam Date
   │
   ▼
Current Date
   │
   ▼
Difference
   │
   ├── Days
   ├── Hours
   ├── Minutes
   └── Seconds
```

---

## Countdown UI

The timer uses SVG circular progress indicators for:

```text id="cb036"
Day
Hour
Minute
Second
```

Each indicator dynamically updates as the exam approaches.

---

## Current TYT Countdown

The current TYT target in the repository is:

```text id="cb037"
20 June 2026
10:15
```

---

## SEO

SEO data is centralized in:

```text id="cb038"
lib/seo.ts
```

Each tool has its own:

```text id="cb039"
Title
Description
Canonical URL
```

---

## Canonical Domain

The SEO configuration references:

```text id="cb040"
https://www.esaspuanhesaplama.com
```

---

## Example Metadata

### Homepage

```text id="cb041"
Esas Puan Hesaplama | LGS, TYT, AYT, YDT
```

### TYT

```text id="cb042"
TYT Puan Hesaplama | Esas Puan Hesaplama
```

### AYT

```text id="cb043"
AYT Puan Hesaplama | Esas Puan Hesaplama
```

### YDT

```text id="cb044"
YDT Puan Hesaplama | Esas Puan Hesaplama
```

---

## Current SEO Data Note

Some metadata strings still reference:

```text id="cb045"
2024
```

while countdown pages already target the **2026 exam period**.

Before production use, these SEO descriptions
