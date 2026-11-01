# Etkinlik & Bilet Rezervasyon Sistemi — Proje Bağlamı

Bu dosya, claude.ai'de yapılan planlama sohbetinin özetidir. Claude bu projede çalışırken önce bunu okumalı.

## Geliştirici

- Hidayet Çiftçi — Bilgisayar Mühendisliği mezunu, Junior Full-Stack Developer.
- Deneyim: React, Next.js, React Native, ASP.NET Core, EF Core, PostgreSQL, Node.js, JWT, Repository Pattern, N-Tier. RAG/Qdrant ile AI projesi.
- Eksik olduğu ve bu projede öğrenmek istediği alanlar: DevOps (Docker, CI/CD, deploy), test yazma, Clean Architecture, arka plan işleri, eşzamanlılık.

## Amaç

- Tamamen öğrenme amaçlı. Kod yazmayı hatırlamak, ardından projeyi uçtan uca canlıya almak.
- Bu Proje 1: basit bir **monolit**, AI yok.
- Proje 2 (sonra konuşulacak): e-ticaret, servislere bölünmüş, AI içeren. Redis, RabbitMQ, mikroservis, mobil ve monitoring o projeye bırakıldı.

## Çalışma şekli (önemli)

- Projeyi Hidayet geliştiriyor, Claude yardımcı/mentor.
- Adım adım ilerlenecek. Her adımda önce "ne yapıyoruz ve neden" açıklanır, sonra kod.
- Hidayet'in kendisi yazabileceği kısımları doğrudan yazıp geçme. Yön göster, yazdıktan sonra review et.
- Yanıtlar Türkçe olacak.
- Bütçe yok: sadece ücretsiz katmanlar kullanılacak.

## Proje

Organizatörlerin kontenjanlı etkinlik oluşturduğu, kullanıcıların yer ayırttığı bir sistem.

- Roller: Admin, Organizatör, Kullanıcı
- Etkinlik CRUD, görsel yükleme, filtreleme, arama, sayfalama
- Rezervasyon: oluştur, onayla, iptal et. Kontenjan aşımı olmamalı (son koltuğa iki kişi aynı anda basarsa).
- Etkinlikten 1 gün önce hatırlatma e-postası (zamanlanmış iş)

## Stack

**Backend**

- ASP.NET Core 10 Web API, EF Core 10, PostgreSQL (Npgsql)
- Mimari: **N-Tier**
- JWT + refresh token
- FluentValidation, Serilog, Hangfire (PostgreSQL storage)
- OpenAPI + Scalar (Swagger yerine)
- Optimistic concurrency: PostgreSQL `xmin`
- MediatR ve AutoMapper KULLANILMAYACAK (ticari lisansa geçtiler). Mapping elle yazılacak.

**Frontend**

- Next.js (App Router) + TypeScript
- Tailwind CSS + shadcn/ui
- TanStack Query, React Hook Form + Zod

**Test**

- xUnit
- Integration test: WebApplicationFactory + Testcontainers (gerçek PostgreSQL)

**DevOps**

- Git + GitHub, feature branch + PR akışı
- Docker + docker-compose (lokalde: api, postgres, mailpit)
- GitHub Actions: PR'da build + test, main'e merge'de image build + deploy
- GitHub Container Registry

**Ücretsiz hosting** (koşullar deploy aşamasında tekrar kontrol edilecek)

- Frontend: Vercel
- API: Render (Docker). 15 dk boşta kalınca uyuyor, Hangfire işleri etkilenir; çözümü deploy aşamasında konuşulacak.
- DB: Neon
- Görseller: Cloudinary
- E-posta: Brevo veya Resend

## Mimari kararı

Hidayet N-Tier'ı biliyor, Clean Architecture'ı öğrenmek istiyor. Bu yüzden:

1. **Önce N-Tier:** `WebAPI → Business → DataAccess → Entities`. Repository arayüzleri DataAccess'te durur.
2. **Auth bitince Clean Architecture'a refactor (2. adımın sonunda, testlerden önce):**
   - Entities → Domain, Business → Application, DataAccess → Infrastructure, WebAPI → Api
   - Arayüzler (`IEventRepository`, `IEmailSender`, `ITokenService`...) Application'a taşınır.
   - Business → DataAccess referansı silinir, Infrastructure → Application referansı eklenir (Dependency Inversion).
   - Application klasörleri türe göre değil özelliğe göre: `Auth/`, `Events/`, `Reservations/`.
   - Repository pattern mi, `IAppDbContext` mi? Bu karar refactor sırasında netleşecek.
3. Refactor'u erteleme: 4-5. adımlarda dış servisler (e-posta, Hangfire, Cloudinary) çoğalınca taşıma maliyeti artar.

## Yol haritası

1. Domain modelleme + veritabanı şeması, solution ve klasör yapısı ← SIRADAKİ ADIM
2. Auth (register, login, JWT, refresh token, roller) → testler
3. Etkinlik CRUD, filtreleme, sayfalama, görsel yükleme + testler
4. Rezervasyon akışı + eşzamanlılık + testler
5. Hangfire ile hatırlatma e-postaları
6. Next.js arayüzü
7. Docker, docker-compose, GitHub Actions
8. Canlıya alma
9. Cilalama: hata yönetimi, loglama, seed data, demo hesap, README

## İlerleme

- [x] Planlama ve stack seçimi (claude.ai)
- [x] Repo yapısı kararı: `backend/` + `frontend/` ayrı klasörler
- [x] Frontend (Next.js) iskeleti kuruldu
- [ ] Backend iskeleti (N-Tier, Visual Studio 2026) ← Hidayet kuruyor
- [ ] Domain modelleme taslağı (entity'ler, alanlar, rezervasyon durumları, kontenjan takibi)
