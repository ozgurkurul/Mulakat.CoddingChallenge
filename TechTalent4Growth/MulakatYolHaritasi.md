# 🧭 TechTalent4Growth (Kees) — Senior Backend Engineer (E-Commerce) Mülakat Yol Haritası

> **Görüşme Türü:** 1. Aşama — Recruiter / Intake & Experience Mapping Görüşmesi (45 dk, İngilizce)
> **Görüşmeci:** Kees (TechTalent4Growth bv — Hollanda)
> **Müşteri:** Hollanda merkezli, 12+ ülkede ve 12 dilde satış yapan, €25M+ cirolu, 100+ çalışanlı DIY (Yapı Market / Hırdavat) E-Ticaret Şirketi
> **Referans Dosyalar:** [JD Senior Backend Engineer e-commerce.pdf](JD%20Senior%20Backend%20Engineer%20e-commerce.pdf) · [Özgür Kurul - CV - TR.pdf](../Özgür%20Kurul%20-%20CV%20-%20TR.pdf) · [AboutMySelf.MD](../AboutMySelf.MD) · [MySelf.MD](../MySelf.MD) · [Summary.md](../net/Summary.md)

---

## 📑 İçindekiler

1. [0. Mülakatın Röntgeni, CV Analizin ve Kees'in Gerçek Amacı](#0-mülakatın-röntgeni-cv-analizin-ve-keesin-gerçek-amacı)
2. [1. Bu Görüşmede Nelere Dikkat Etmelisin? (12 Altın Kural)](#1-bu-görüşmede-nelere-dikkat-etmelisin-12-altın-kural)
3. [2. Kendini Anlatırken Kullanacağın Her Şey (CV'ndeki Gerçek Rakamlarla)](#2-kendini-anlatırken-kullanacağın-her-şey-cvndeki-gerçek-rakamlarla)
   - [2.1. 90 Saniyelik Ana Açılış ("Tell me about yourself")](#21-90-saniyelik-ana-açılış-tell-me-about-yourself)
   - [2.2. CV'ndeki Şirketleri Tek Tek Anlatırken Kullanacağın Kartlar](#22-cvndeki-şirketleri-tek-tek-anlatırken-kullanacağın-kartlar)
4. [3. İlandaki Teknolojileri CV'ndeki Kanıtlara Bağlama Tablosu (Cheat Sheet)](#3-ilandaki-teknolojileri-cvndeki-kanıtlara-bağlama-tablosu-cheat-sheet)
5. [4. Gelebilecek Tüm Sorular ve Hazır İngilizce Yanıtları](#4-gelebilecek-tüm-sorular-ve-hazır-ingilizce-yanıtları)
   - [A. Motivasyon, Rol Uyumu ve Kariyer Soruları](#a-motivasyon-rol-uyumu-ve-kariyer-soruları)
   - [B. İlanda Vurgulanan 4 Kritik "Sistem ve Entegrasyon" Sorusu](#b-ilanda-vurgulanan-4-kritik-sistem-ve-entegrasyon-sorusu)
   - [C. Teknoloji Yığını (Tech Stack), Observability ve AI-Native Soruları](#c-teknoloji-yığını-tech-stack-observability-ve-ai-native-soruları)
   - [D. İletişim, Remote Çalışma ve Davranışsal (STAR) Sorular](#d-iletişim-remote-çalışma-ve-davranışsal-star-sorular)
   - [E. Lojistik, Çalışma Saatleri ve Ücret (Monthly Fee) Soruları](#e-lojistik-çalışma-saatleri-ve-ücret-monthly-fee-soruları)
6. [5. Toplantı Sonunda Kees'e Soracağın Sorular](#5-toplantı-sonunda-keese-soracağın-sorular)
7. [6. Görüşme Anında Açık Tutacağın "1 Bakışta Kopya Kağıdı"](#6-görüşme-anında-açık-tutacağın-1-bakışta-kopya-kağıdı)

---

## 0. Mülakatın Röntgeni, CV Analizin ve Kees'in Gerçek Amacı

### 0.1. Kees'in Cümlesinin Şifresi
> *"The interview is not deeply technical. We want to understand your experience and if we proceed we use this information to present you to our client."*

1. **Derin Kodlama Sınavı Yok, "Kanıt Toplama" Var:** Kees bu 45 dakikada senin ağzından çıkacak **somut rakamları, şirket isimlerini ve mimari çözümleri** not alıp Hollandalı müşteriye (CTO ve Engineering Lead'e) sunacağı **Candidate Presentation (Aday Sunum Raporu)** dosyasını yazacak.
2. **CV'ndeki Rakamlar Bu İlan İçin Altın Değerinde:** CV'ni incelediğimde ilandaki her maddenin birebir sayısal kanıtı olduğunu gördüm:
   - **Yemeksepeti Mahalle:** Günde **10.000'lerce sipariş** işleyen quick-commerce platformunun **Sipariş (Order) ve Kampanya (Campaign)** mikroservislerini **.NET ve PostgreSQL** ile sıfırdan yazman (İlanın ana stack'i birebir **.NET + PostgreSQL**!). Üstelik **Locust ile 500.000 istek** yük testi ve **Datadog + Redis** optimizasyonu.
   - **Delivery Hero Tech Hub (Integration Hub):** **25 kişilik** global Integration Hub ekibinde Türkiye'de **~750**, Asya-Pasifik'te **~1.500 (toplam 2.250+)** entegratör firmanın API geçişlerini test ettiği **Sandbox Test Ortamını** geliştirmen.
   - **DeFacto:** Global omnichannel e-ticarette **online sipariş yönetimi, otomatik kurye atama, iade lojistiği (return logistics)** ve **pazaryeri (marketplace) / kargo** entegrasyonları.
   - **Novartz:** **MetalAVM** e-ticaret platformunu uçtan uca kurman + B2B müşteriler için **15+ CRM ve CMS** teslim etmen + **Banka Payment Gateway (PSP)** ve **LOGO / E-Flow gibi ERP sistemlerini** entegre etmen! (İlanda aranan *ERP + CMS + PSP + E-Commerce* dörtlüsünü burada bizzat yapmışsın!).
   - **Halkbank:** Finansal işlemlerde yüksek güvenlik (**Security & Compliance**) ve **API Gateway** mimarisi.
   - **Drivee Teknoloji:** Günde **25 milyon veri paketi / 10.000 cihaz**, monolitten **.NET 10** mikroservislere geçiş, **DDD, Clean Architecture, Vertical Slicing, Outbox Pattern, Debezium (CDC), Kafka, Hangfire, PostgreSQL**, **React/TypeScript** ve **Claude Code / Copilot Review / MCP** ile **1 günlük PR review beklemesini sıfıra indiren** AI-native iş akışı!

### 0.2. İlan ↔ CV Eşleşme Matrisi (Kees'in Tik Atacağı Kutular)

| İlandaki Gereksinim | CV'ndeki Birebir Kanıtın | Eşleşme |
|---|---|---|
| **Senior C#/.NET Backend (5+ yıl)** | **10+ yıl** C#/.NET (.NET Framework'ten **.NET 10**'a kadar; ASP.NET Core Web API, EF Core, Dapper, Hangfire). | ✅ **%100** |
| **Full-Stack (.NET güçlü + React/Redux artı)** | İlan S.3: *"If you are a full-stack engineer with particularly strong .NET/backend expertise, we would also like you to consider this position."* → CV başlığın: **Senior Software Engineer \| Full Stack (.NET + React/TypeScript)**. | ✅ **%100** |
| **E-Commerce & High-Volume Transactions** | **Yemeksepeti Mahalle** (günde 10 binlerce sipariş, 500k load test), **DeFacto** (global omnichannel e-ticaret), **MetalAVM** (hırdavat/metal e-ticaret). | ✅ **%100** |
| **ERP, PIM, CMS, Feed, PSP Entegrasyonu** | **Novartz** (LOGO & E-Flow **ERP**, Banka **PSP/Payment Gateways**, **15+ CMS/CRM**), **DeFacto** (Marketplace/Feed & Kargo), **Delivery Hero** (2.250+ entegratör). | ✅ **%100** |
| **Event-Driven & Message Brokers** | **Kafka, Debezium (CDC), Outbox Pattern** (Drivee - 25M paket/gün), **RabbitMQ**, **AWS SNS/SQS** (Yemeksepeti & DH). | ✅ **%100** |
| **PostgreSQL** | **Yemeksepeti Mahalle** (sipariş & kampanya DB'si olarak PostgreSQL!) + **Drivee** (PostgreSQL / PostGIS). | ✅ **%100** |
| **Observability & Monitoring** | **Datadog** (DH & Yemeksepeti trace/latency analizi), **Elastic APM, Elasticsearch, Graylog, Health Checks** (Drivee). | ✅ **%100** |
| **Automated Testing & Quality Gates** | **Delivery Hero** (2.250 entegratör için Sandbox test ortamı + unit test), **Drivee** (GitHub Actions, SonarQube, Copilot Review). | ✅ **%100** |
| **Avrupa Kültürü, İngilizce & GDPR** | **Erasmus+ (Krakow, Polonya - AGH University)** + **Delivery Hero** (%100 İngilizce global ekip) + **GDPR Compliance (2024)** & **Secure Code Warrior** sertifikaları. | ✅ **Gizli Koz!** |

---

## 1. Bu Görüşmede Nelere Dikkat Etmelisin? (12 Altın Kural)

1. **CV'ndeki "Senior Full Stack Engineer (Strong .NET)" Konumlanması İlanla Birebir Örtüşüyor:**
   - İlanın 3. sayfasında özel bir not var: *"If you are a full-stack engineer with particularly strong .NET/backend expertise, we would also like you to consider this position."*
   - Kees'e şunu net söyle: *"My core strength—about 75-80% of my career—is heavy backend architecture, .NET, and distributed integrations. Having React and TypeScript skills simply makes me a much better partner for the two frontend engineers on the team."*

2. **Drivee'deki "6 Kişilik Ekibin Teknik Yönü" İfadesini "Hands-On" Olarak Açıkla:**
   - İlanda *"You do not need experience leading or managing a team. Your seniority should show in your technical judgement, architecture understanding, and ownership — not people management"* deniyor.
   - Kees'e Drivee'de İK/idari yöneticilik yapmadığını, **kodun ve mimarinin içinde olan teknik öncü (hands-on senior engineer)** olduğunu, zamanının çoğunu **.NET 10, Kafka, Outbox Pattern ve kod kalitesine** harcadığını vurgula.

3. **Yemeksepeti Mahalle'de ".NET + PostgreSQL" İkilisini Mutlaka Söyle:**
   - Müşterinin backend teknoloji yığını **.NET + PostgreSQL**.
   - Sen **Yemeksepeti Mahalle**'de günde on binlerce sipariş alan quick-commerce sisteminin **Order (Sipariş) ve Campaign (Kampanya)** mikroservislerini bizzat **.NET ve PostgreSQL** ile sıfırdan yazdın! Kees bunu duyduğunda notlarına yıldızlı olarak yazacaktır.

4. **Novartz Deneyimini "ERP + CMS + Payment Gateway + MetalAVM" Olarak Parlat:**
   - Genelde adaylar ilk iş yerlerini hızlı geçer ama senin **Novartz** deneyimin bu ilan için bir hazine:
     - **LOGO ve E-Flow ERP** entegrasyonları yaptın (Müşterinin **NetSuite ERP** beklentisinin tam karşılığı).
     - **Banka Payment Gateway (PSP)** entegrasyonları yaptın.
     - **15'ten fazla CMS ve CRM** teslim ettin (Müşterinin **Prismic CMS / Akeneo PIM** beklentisinin karşılığı).
     - **MetalAVM** e-ticaret sitesini uçtan uca geliştirdin (Müşteri de **DIY / yapı-market / metal donanım** satıyor!).

5. **Delivery Hero'daki "750 Türkiye + 1.500 APAC Entegratörü" Rakamını Kullan:**
   - Avrupalı recruiter'lar somut ölçek (scale) rakamlarına bayılır. *"At Delivery Hero's 25-person Integration Hub, I built a sandbox testing platform used by 750 integrators in Turkey and 1,500 in Asia-Pacific"* cümlesi seni anında "Senior Integration Expert" seviyesine taşır.

6. **Halkbank ve Sertifikalarını "Transactional Security & GDPR" İçin Kullan:**
   - İlanın 3. sayfasında *"Security and reliable processing of transactional data"* maddesi var.
   - Kısa süreli **Halkbank** deneyimini, **GDPR Compliance (2024)** ve **Secure Code Warrior** sertifikalarını *"Avrupa veri koruma (GDPR) ve finansal/transactional güvenlik standartlarına hakimim"* demek için kullan.

7. **Polonya Erasmus+ (AGH University) Detayını Sohbete Sıkıştır:**
   - TechTalent4Growth'un ilandaki son sayfasında *"Cultural fit zone"* vurgusu var. Kees'e üniversitedeyken **Polonya Krakow'da (AGH University) Erasmus+** yaptığını ve **Delivery Hero**'da Avrupa ekipleriyle çalıştığını söylemek, Avrupa çalışma kültürüne tamamen aşina olduğunu kanıtlar.

8. **Observability (Datadog, Elastic APM, Graylog) Vurgusu Yap:**
   - İlanın 6. sayfasında *"Improve monitoring, logging and observability so problems can be identified and diagnosed quickly"* yazıyor.
   - CV'nde **Datadog** (Yemeksepeti'nde trace analiziyle latency çözümü) ve **Elastic APM / Elasticsearch / Graylog** (Drivee) var. Kees'e *"Ben sadece kodu yazıp bırakmam; Datadog ve Elastic APM ile canlıda izler, darboğazları bulup çözerim"* mesajını ver.

9. **AI-Native Çalışma Biçimini "Hız ve Kalite Çarpanı" Olarak Anlat:**
   - Müşteri, dış ajanstan (external agency) işi devralıp geliştirmeyi hızlandırmak (*"speed up development"*) istiyor.
   - Drivee'de **Claude Code, GitHub Copilot Review ve MCP (PostgreSQL/GitHub)** kullanarak **1 günlük PR review bekleme süresini ortadan kaldırdığını** ve legacy modülleri hızla refactoryip **SonarQube** ile test coverage'ı artırdığını anlat. Bu, CTO'nun gözlerini parlatacak bir detaydır.

10. **Drivee Metriklerinde CV'ndeki Sayılarla Tutarlı Ol:**
    - CV'nde Drivee için **"günde 25 milyon veri paketi, 10.000 araç/cihaz, 80 KFC/HD İskender/Pidem şubesi, .NET 10"** yazıyor. Mülakatta da (eski notlardaki 50M yerine) CV'yle birebir tutarlı olarak **"25 million data packets per day across 10,000 devices"** ve **".NET 10"** ifadelerini kullan.

11. **Kees'e İngilizce CV Gönderirken:**
    - Kees mesajında *"Please provide me with your CV"* dedi. Elindeki Türkçe CV'nin (`Özgür Kurul - CV - TR.pdf`) birebir İngilizce versiyonunu gönderdiğinden emin ol (istersen bu görüşme rehberinden sonra İngilizce CV metnini de bu ilandaki anahtar kelimelerle optimize edebiliriz).

12. **İletişim Tarzı:**
    - Net, sakin, gülümseyen ve yapılandırılmış konuş. Uzun monologlar yerine 60–90 saniyelik net cevaplar verip *"Does that answer your question, or would you like me to elaborate on the technical side?"* diye sor.

---

## 2. Kendini Anlatırken Kullanacağın Her Şey (CV'ndeki Gerçek Rakamlarla)

### 2.1. 90 Saniyelik Ana Açılış ("Tell me about yourself")

#### 🦴 Göz Ucuyla Bakmalık İskelet (Akış)
1. **Özet Kimlik:** 10+ years hands-on Senior Engineer in **C#/.NET** -> Focus: **High-volume E-commerce, Complex 3rd-Party Integrations, Event-Driven Systems** (Plus Erasmus in Poland & global English teams).
2. **Temel & ERP/E-Ticaret (Novartz & DeFacto):** Built **MetalAVM** (hardware e-commerce!) and integrated **ERPs (LOGO/E-Flow), CMSs, and Bank Payment Gateways**. Then at **DeFacto** (global omnichannel e-commerce), built order management, marketplace integrations, and return logistics.
3. **Yüksek Ölçekli Sipariş & PostgreSQL (Yemeksepeti Mahalle):** Built quick-commerce **Order & Campaign microservices from scratch using .NET and PostgreSQL** (tens of thousands of daily orders, AWS SNS/SQS event-driven, load-tested to **500k requests** with Locust & Datadog).
4. **Global Entegrasyon Merkezi (Delivery Hero Tech Hub):** In a 25-person global English team, built integration services (.NET, Go, SQS) and a **Sandbox Testing Platform for 2,250+ third-party integrators** across Turkey and APAC.
5. **Güncel Mimari Dönüşüm (Drivee):** Currently hands-on Senior Engineer at Drivee -> Migrated a legacy monolith to **.NET 10** microservices using **DDD, Clean Architecture, Outbox Pattern, Debezium CDC, Kafka, and PostgreSQL** (processing **25M packets/day**), plus AI-native CI/CD workflows.

#### 🎤 Tam İngilizce Konuşma Metni (CV'ndeki Rakamlarla Birebir Uyumlu)

> *"Thank you for your time, Kees. Let me give you a structured overview of my background.*
>
> *I am a hands-on Senior Software Engineer with over **10 years of experience in the C# and .NET ecosystem**, focusing heavily on **e-commerce platforms, complex third-party integrations, and high-volume event-driven architectures**.*
>
> *In the first phase of my career at **Novartz**, I built an end-to-end e-commerce platform called **MetalAVM**—which actually sold hardware and metal products, very similar to your client's DIY market—and I delivered over 15 B2B portals where I integrated **ERP systems (like LOGO and E-Flow), CMS platforms, and bank payment gateways**.*
>
> *Then I moved into massive-scale e-commerce at **DeFacto**, a global omnichannel fashion brand. There, I developed core backend services covering online order management, marketplace and cargo integrations, automatic courier assignment, and return logistics.*
>
> *After DeFacto, I joined **Yemeksepeti**, where I built the **Order and Campaign microservices from scratch using .NET and PostgreSQL** for their quick-commerce platform, handling tens of thousands of daily orders. We designed the entire order lifecycle as an event-driven architecture on AWS SNS and SQS, optimized latency using Datadog traces and Redis caching, and verified the platform up to **500,000 requests** with Locust load tests.*
>
> *Following that success, I moved to **Delivery Hero’s Global Tech Hub**—a 100% English-speaking, distributed team. Working in a 25-person Integration Hub team, I built event-driven integration services and created a centralized **sandbox testing platform used by over 2,200 third-party integrators**—around 750 in Turkey and 1,500 in the Asia-Pacific region—to test their API migrations safely.*
>
> *After a brief contract at **Halkbank** building high-security financial microservices, I joined **Drivee Technology** where I work today. At Drivee, I led the architectural transformation of a legacy monolith into modern **.NET 10 microservices** using **DDD, Clean Architecture, Outbox Pattern, Debezium CDC, Kafka, and PostgreSQL**, reliably processing **25 million data packets per day**. I also use React and TypeScript on the frontend and integrated AI-native code review and CI/CD pipelines with GitHub Actions and SonarQube.*
>
> *When I read your job description, I saw an exact match with my career: building a reliable .NET integration layer on PostgreSQL and message queues that connects e-commerce, ERP, PIM, and payment systems, and taking full ownership of production reliability."*

---

### 2.2. CV'ndeki Şirketleri Tek Tek Anlatırken Kullanacağın Kartlar

Kees CV'ne bakıp *"Tell me more about..."* dediğinde kullanacağın hazır paragraflar:

#### 🏢 1. Drivee Teknoloji (Şubat 2024 – Günümüz)
- **Vurgulanacaklar:** Monolith → **.NET 10** Microservices, **DDD, Clean Architecture, Outbox Pattern, Debezium (CDC), Kafka, Hangfire, PostgreSQL**, **25M paket/gün**, **Elastic APM / Elasticsearch**, **GitHub Actions + SonarQube + Copilot Review + MCP**.
- **İngilizce Anlatım:**
  > *"At Drivee, I am the technical lead and hands-on senior engineer for a platform processing 25 million data packets daily across 10,000 connected devices and 80+ enterprise locations like KFC. When I joined, it was a tightly coupled monolith. I broke it down into loosely coupled **.NET 10 microservices** using **Domain-Driven Design (DDD), Clean Architecture, and Vertical Slicing**. To guarantee zero data loss between services, I implemented the **Transactional Outbox Pattern with Debezium CDC and Kafka**, alongside **Hangfire** for background jobs and **PostgreSQL** for storage. I also modernized our CI/CD with GitHub Actions, SonarQube, and automated GitHub Copilot PR reviews, which eliminated a 1-day code review bottleneck."*

#### 🏢 2. Halkbank (Ekim 2023 – Şubat 2024)
- **Vurgulanacaklar:** Kısa dönemli kritik proje (4-5 ay), **API Gateway**, **Transactional Security & Compliance** (İlandaki *"Security and reliable processing of transactional data"* maddesi için).
- **İngilizce Anlatım:**
  > *"Halkbank was a focused banking project where I developed high-performance backend microservices for their Campus & Youth Banking mobile app and migrated core banking capabilities behind an API Gateway. It reinforced my discipline around strict financial security, data compliance, and fault-tolerant transactional APIs."*

#### 🏢 3. Delivery Hero Tech Hub — Integration Hub (Nisan 2022 – Ekim 2023)
- **Vurgulanacaklar:** **25 kişilik Integration Hub**, **750 TR + 1.500 APAC entegratörü**, **Sandbox Test Ortamı**, **.NET, Go, AWS SQS/Lambda/DynamoDB, Kubernetes, Datadog**, **RFC dokümanları**, **%100 İngilizce dağıtık ekip**.
- **İngilizce Anlatım:**
  > *"At Delivery Hero's Global Tech Hub, I worked in a 25-engineer Integration Hub team entirely in English with product teams across Europe and Asia. Our mission was managing the integration layer between Delivery Hero's core ordering platform and thousands of external partner systems—ERPs, POS systems, and logistics providers. I developed resilient integration services using .NET, Go, and AWS SQS, monitored with Datadog on Kubernetes. My proudest contribution there was co-authoring the RFC and building a **sandbox integration testing environment** that allowed **750 partners in Turkey and 1,500 partners in APAC** to self-test their API migrations before going to production."*

#### 🏢 4. Yemeksepeti — Yemeksepeti Mahalle (Ağustos 2021 – Nisan 2022)
- **Vurgulanacaklar:** **.NET + PostgreSQL**, **Order & Campaign Microservices**, **Günde 10.000'lerce sipariş**, **AWS SNS/SQS Event-Driven**, **Datadog + Redis**, **500.000 istek Locust Load Test**.
- **İngilizce Anlatım:**
  > *"In the Yemeksepeti Mahalle quick-commerce project, I built the core **Order and Campaign microservices from scratch using .NET and PostgreSQL**—which is the exact same core stack your client uses. We processed tens of thousands of orders per day. Every step from checkout to payment, warehouse picking, and courier delivery ran asynchronously over **AWS SNS and SQS**. I also used **Datadog distributed tracing** to find latency bottlenecks, added **Redis distributed caching**, and validated the system under heavy load up to **500,000 requests using Locust**."*

#### 🏢 5. DeFacto (Ağustos 2020 – Ağustos 2021)
- **Vurgulanacaklar:** **Global Omnichannel E-Commerce**, **Order Management**, **Marketplace (Feed) & Cargo/Courier Integrations**, **Return Logistics (İade süreçleri)**.
- **İngilizce Anlatım:**
  > *"DeFacto is a global omnichannel e-commerce retailer operating across many countries, very similar to your client's multi-country footprint. I worked on the backend services managing the full online order lifecycle—from marketplace order ingestion and stock allocation to automatic courier assignment and return/refund logistics."*

#### 🏢 6. Novartz (Ocak 2016 – Temmuz 2020)
- **Vurgulanacaklar:** **MetalAVM (E-commerce)**, **LOGO & E-Flow ERP Integrations**, **Bank Payment Gateways (PSPs)**, **15+ CMS & CRM systems**.
- **İngilizce Anlatım:**
  > *"Novartz is where I built my foundational breadth. I developed **MetalAVM**, an end-to-end e-commerce platform for hardware and metal products, and delivered over 15 enterprise B2B portals, CMSs, and CRMs. Crucially for this role, I integrated multiple **bank payment gateways (PSPs)** and **ERP systems like LOGO and E-Flow** to synchronize invoices, inventory, and accounting data."*

---

## 3. İlandaki Teknolojileri CV'ndeki Kanıtlara Bağlama Tablosu (Cheat Sheet)

| İlandaki Sistem / Gereksinim | CV'ndeki Somut Karşılığın | Kees'e Söyleyeceğin Hazır İngilizce Cümle |
|---|---|---|
| **C# / .NET** | 10+ yıl, **.NET 10**, ASP.NET Core Web API, EF Core, Dapper, Hangfire, SignalR | *"I have 10+ years of production C#/.NET experience and I've already upgraded our production microservices at Drivee to **.NET 10**, using EF Core, Dapper, and Hangfire."* |
| **PostgreSQL** | **Yemeksepeti Mahalle** (Sipariş & Kampanya DB) + **Drivee** (PostgreSQL / PostGIS) | *"I built Yemeksepeti's Order and Campaign microservices on **PostgreSQL**, and I currently use PostgreSQL daily at Drivee for high-volume transactional data."* |
| **BigCommerce** (E-commerce) | **Yemeksepeti Mahalle**, **DeFacto**, **MetalAVM** | *"While I haven't used BigCommerce by name, I built e-commerce order, cart, and campaign engines from scratch at Yemeksepeti and DeFacto, so I know e-commerce webhooks and APIs inside out."* |
| **NetSuite** (ERP) | **LOGO & E-Flow ERP** (Novartz) + **DeFacto** ERP/Depo + **DH** POS/ERP entegrasyonları | *"I have directly integrated ERP systems like LOGO and E-Flow as well as retail ERPs at DeFacto to sync orders, stock, and invoices asynchronously."* |
| **Akeneo (PIM) & Prismic (CMS)** | **15+ CMS/CRM** (Novartz) + Çok ülkeli katalog (DeFacto) | *"I've built and integrated over 15 CMS and catalog systems. I understand how Akeneo and Prismic act as the source of truth for product enrichment and multi-language content across 12 countries."* |
| **Channable** (Marketplace / Feed) | **DeFacto** Marketplace entegrasyonları + **DH** 2.250 entegratör | *"At DeFacto, I worked directly on marketplace integrations—syncing product feeds, stock, and pulling multi-channel orders into the core platform."* |
| **Payment Providers (PSPs)** | **Novartz** Banka Payment Gateways + **Yemeksepeti** + **Halkbank** | *"I have integrated multiple bank payment gateways and PSPs, handling payment capture, refunds, webhooks, and idempotent transaction processing."* |
| **Message Brokers & Event-Driven** | **Kafka, Debezium (CDC), Outbox Pattern, RabbitMQ, AWS SNS/SQS** | *"I actively use **Kafka, Debezium CDC, RabbitMQ, and AWS SNS/SQS** in production with the **Transactional Outbox Pattern** to guarantee reliable asynchronous messaging."* |
| **Observability & Monitoring** | **Datadog, Elastic APM, Elasticsearch, Graylog, Health Checks** | *"I have hands-on experience with **Datadog, Elastic APM, Elasticsearch, and Graylog**—using distributed tracing to diagnose latency and cross-system failures quickly."* |
| **Testing & Quality** | **DH Sandbox** (2.250 entegratör), **Locust** (500k load test), **SonarQube**, Unit/Integration tests | *"Beyond unit and integration tests with SonarQube quality gates, I built an integration sandbox for 2,250 partners at Delivery Hero and ran 500k-request load tests with Locust."* |
| **Cloud, Docker & CI/CD** | **AWS, Docker, Kubernetes, GitHub Actions, GoCD, Nginx/Kong/HAProxy, WAF/CDN** | *"I run production workloads on Docker, Kubernetes, and cloud with GitHub Actions and GoCD pipelines. While my heaviest enterprise cloud experience is AWS and containerized Linux, adapting to Azure and Fly.io for .NET containers is very natural for me."* |
| **React, Redux & Tailwind** | **React, Next.js, TypeScript, React Native (Expo)** | *"I actively use React and TypeScript at Drivee, which fits your note about full-stack engineers with strong .NET backend expertise—I can collaborate seamlessly with the 2 frontend engineers."* |

---

## 4. Gelebilecek Tüm Sorular ve Hazır İngilizce Yanıtları

---

### A. Motivasyon, Rol Uyumu ve Kariyer Soruları

#### ❓ Soru 1: *"Why are you interested in this position, and why are you looking to leave Drivee?"*
*(Neden bu pozisyonla ilgileniyorsun ve neden Drivee'den ayrılmak istiyorsun?)*

**🎤 İngilizce Cevap:**
> *"At Drivee, I achieved the main goal I was hired for: I transformed their legacy monolithic application into a modern **.NET 10** event-driven microservice architecture with Kafka and PostgreSQL, and automated their CI/CD and code review pipelines. The platform is now stable and running smoothly.*
>
> *Now, I want to return to an international, English-speaking engineering environment—like I had at Delivery Hero and during my Erasmus studies in Poland—and focus 100% on **e-commerce and complex system integrations**, which is my strongest domain. Helping a fast-growing Dutch e-commerce company transition their platform in-house from an external agency is an exciting, long-term challenge where I can bring immediate value."*

---

#### ❓ Soru 2: *"At Drivee, you guide a 6-person team and your title is Senior Full Stack Engineer. Here, we need a hands-on Senior Backend Engineer (no people management, heavy .NET focus). Does that fit your ambition?"*
*(Drivee'de 6 kişilik ekibin teknik yönünü belirliyorsun ve Full Stack unvanın var. Biz ise insan yönetimi olmayan, ağır .NET odaklı bir hands-on Senior Backend Engineer arıyoruz. Bu senin hedefinle uyuşuyor mu?)*

**🎤 İngilizce Cevap:**
> *"Yes, 100%. First, regarding the Full Stack title: my core expertise—around 80% of my career at Delivery Hero, Yemeksepeti, Halkbank, and DeFacto—has always been **heavy .NET backend engineering, distributed systems, and integrations**. Knowing React and TypeScript simply helps me understand the full picture and collaborate smoothly with frontend engineers without friction.*
>
> *Second, regarding leadership: I am a **hands-on builder**, not a people manager. Even at Drivee, I spend most of my day writing C# code, designing Kafka and Outbox flows, and optimizing PostgreSQL queries. I don't want to move into management meetings—I want to stay hands-on, solve complex integration problems, and contribute to architectural decisions alongside the Engineering Lead."*

---

#### ❓ Soru 3: *"I see Halkbank on your CV for 5 months (Oct 2023 – Feb 2024). Why was that period relatively short?"*
*(CV'nde Halkbank 5 ay görünüyor. Neden o süre nispeten kısaydı?)*

> **🎯 Strateji:** Recruiter'lar CV'deki kısa süreli geçişleri mutlaka sorar. Çok net ve pozitif bir açıklaması var: Halkbank'taki Genç/Kampüs Bankacılığı mikroservis ve API Gateway geçiş projesini tamamladın, ardından ilk mimarisini yıllar önce (Novartz döneminde) kurduğun Drivee'den tüm platformu monolitten mikroservise dönüştürmen için çok cazip bir mimari sahiplik teklifi geldi!

**🎤 İngilizce Cevap:**
> *"At Halkbank, I joined to deliver a specific scope: building the backend microservices and API Gateway migration for their Youth & Campus Banking mobile app. While I was completing that delivery, the leadership at Drivee—a product whose very first architecture I had built years ago—reached out to me with a great opportunity to take end-to-end technical ownership of their monolith-to-microservice transformation. Since banking has a slower, more bureaucratic pace and I thrive in fast-moving, high-ownership environments, I decided to take on the transformation challenge at Drivee."*

---

### B. İlanda Vurgulanan 4 Kritik "Sistem ve Entegrasyon" Sorusu

> [!IMPORTANT]
> İlanın 7. sayfasında bizzat yazan bu 4 soruyu yanıtlarken CV'ndeki **Outbox Pattern, Debezium, Kafka, AWS SQS, Datadog ve Yemeksepeti/DeFacto** örneklerini referans ver!

---

#### ❓ Soru 4: *"What happens when a customer successfully pays on the webshop, but the ERP (NetSuite) is temporarily unavailable?"*
*(Müşteri webshop'ta ödemeyi başarıyla yaptı ama o sırada ERP geçici olarak ulaşılamıyor. Ne yaparsın?)*

**🎤 İngilizce Cevap:**
> *"Customer checkout and payment capture must **never** fail just because a back-office ERP like NetSuite is temporarily down or throttling requests.*
>
> *I handle this using the **Transactional Outbox Pattern** and a **Message Broker**—just like I implemented with PostgreSQL, Debezium/Outbox, and message queues at Drivee and Yemeksepeti.*
>
> *When the payment succeeds, our .NET integration layer saves the order state and writes an `OrderPaid` event into the outbox table in the same database transaction, then immediately returns success to the webshop. Asynchronously, a queue consumer picks up the event and calls NetSuite.*
>
> *If NetSuite is down or returns a `503` or `429`, we apply **exponential backoff with jitter**. If it fails repeatedly beyond the retry threshold, the message goes to a **Dead-Letter Queue (DLQ)**, triggers an alert in our monitoring tool, and is automatically or manually replayed once NetSuite is healthy again. That way, we achieve **eventual consistency** with zero lost orders."*

---

#### ❓ Soru 5: *"How do you prevent the same order or payment from being processed twice?"*
*(Aynı siparişin veya ödemenin iki kez işlenmesini nasıl önlersin?)*

**🎤 İngilizce Cevap:**
> *"In distributed systems and webhook integrations, duplicate messages are inevitable because message brokers use **at-least-once delivery**. The solution is strict **Idempotency** at every boundary.*
>
> *In our .NET integration services, every order or payment event carries a deterministic `IdempotencyKey`—such as the `BigCommerceOrderId` or `PaymentTransactionId`. In **PostgreSQL**, we enforce a **unique constraint** on that key in an Inbox or Processed Events table.*
>
> *When a duplicate webhook or retry arrives, the database check immediately detects that it was already processed (or is currently locked in progress) and returns a safe acknowledgement without creating a second order in NetSuite or charging the customer twice."*

---

#### ❓ Soru 6: *"What happens when inventory changes simultaneously in the ERP, webshop, and marketplaces?"*
*(Stok aynı anda ERP'de, webshop'ta ve pazaryerlerinde değişirse ne olur?)*

**🎤 İngilizce Cevap:**
> *"I dealt with this exact problem at **DeFacto** across marketplaces and at **Yemeksepeti Mahalle** across dark stores.*
>
> *First, we establish a **Single Source of Truth**: NetSuite (ERP) owns the physical warehouse stock, while the .NET integration layer tracks real-time **stock reservations** for in-flight orders.*
>
> *Second, we process stock updates as **ordered, event-driven deltas** per SKU (or use optimistic concurrency with version numbers/timestamps in PostgreSQL) so an older sync message never overwrites a newer stock count.*
>
> *Third, when publishing stock feeds to external channels via **Channable**, we calculate `Available-to-Sell = Physical Stock - Reserved Stock - Safety Buffer`. Keeping a small safety buffer for fast-moving DIY items prevents overselling when two customers on different channels buy the last item at the same second."*

---

#### ❓ Soru 7: *"How do you recover when one part of a multi-system process fails, and how do you diagnose it quickly?"*
*(Çoklu sistem sürecinin bir parçası çöktüğünde nasıl kurtarırsın ve sorunu nasıl hızlıca teşhis edersin?)*

**🎤 İngilizce Cevap:**
> *"At Yemeksepeti and Delivery Hero, we used **Datadog**, and at Drivee I set up **Elastic APM, Elasticsearch, and Graylog**. To diagnose issues in seconds across 5 or 6 systems, every request and message must propagate a **Correlation ID (Trace ID)**.*
>
> *When one step of a multi-system flow fails, we first separate **transient network failures** (handled by automated retries and circuit breakers) from **data/business failures** (routed to a Dead-Letter Queue with immediate alerting).*
>
> *For recovery, we design **compensating actions (Saga pattern)**—for example, releasing a stock reservation if payment fails—and run scheduled **reconciliation jobs** (using Hangfire) to verify that BigCommerce orders, PSP captures, and NetSuite invoices are 100% in sync."*

---

### C. Teknoloji Yığını (Tech Stack), Observability ve AI-Native Soruları

#### ❓ Soru 8: *"Tell me about your experience with automated testing and how you ensure code quality before going to production."*
*(Otomatik test deneyimini ve canlıya çıkmadan önce kod kalitesini nasıl sağladığını anlatır mısın?)*

**🎤 İngilizce Cevap:**
> *"I treat testing and quality gates as part of the architecture:*
>
> *1. **Unit & Integration Tests:** Using **DDD and Clean Architecture**, I keep domain logic isolated so it's easy to unit test. In our **GitHub Actions** pipeline, every PR must pass unit tests and **SonarQube** quality gates.*
> *2. **End-to-End Integration Sandbox:** At **Delivery Hero**, I built a complete **Sandbox Testing Platform** used by **2,250+ external integrators** to test their API flows end-to-end before production.*
> *3. **Load Testing:** At **Yemeksepeti**, I wrote **Locust** load tests to prove our .NET and PostgreSQL order services could handle **500,000 requests** without degradation.*
> *4. **AI-Assisted Code Review:** At Drivee, I integrated **GitHub Copilot Review** and custom rule files into our PR workflow, which catches edge cases early and eliminated a 1-day review wait time."*

---

#### ❓ Soru 9: *"I see on your CV that you focus heavily on 'AI-Native Development' (Claude Code, Copilot, MCP). How does that help you in a Senior Backend Engineer role like this?"*
*(CV'nde AI-Native Development vurgusu var. Bu, böyle bir Senior Backend rolünde sana nasıl yardımcı oluyor?)*

> **🎯 Strateji:** Müşterinin amacı **2026 sonuna kadar dış ajanstan işi devralıp geliştirmeyi hızlandırmak ("speed up development")**. AI araçlarını "kod uydurmak" için değil, **legacy/ajans kodunu hızlı analiz etmek, dokümantasyon çıkarmak, test yazmak ve PR review süresini kısaltmak** için kullandığını anlat!

**🎤 İngilizce Cevap:**
> *"For a team that needs to take over an existing codebase from an external agency before the end of 2026, AI-native workflows are a massive productivity multiplier—when used with strict engineering discipline.*
>
> *At Drivee, I use **Claude Code, GitHub Copilot, and MCP servers (connected to PostgreSQL and GitHub)** with strict project-specific architecture rules. It helps me map out unfamiliar legacy codebases rapidly, generate comprehensive unit and integration tests, keep API documentation up to date, and automate the first pass of PR reviews.*
>
> *Of course, I never blindly trust AI output—every line is validated against **Clean Architecture rules, SonarQube, and automated tests**. It simply allows me as a senior engineer to deliver clean, well-tested .NET code much faster."*

---

### D. İletişim, Remote Çalışma ve Davranışsal (STAR) Sorular

#### ❓ Soru 10: *"How is your experience working remotely in English with European teams?"*
*(Avrupa ekipleriyle İngilizce ve uzaktan çalışma deneyimin nasıl?)*

**🎤 İngilizce Cevap:**
> *"Very strong and natural for me. Actually, my international experience started during my university years when I studied Computer Science in **Krakow, Poland (AGH University)** through the Erasmus+ program.*
>
> *Professionally, at **Delivery Hero Tech Hub**, I worked 100% remotely in English for over two years in a distributed team spanning Berlin, Istanbul, and Asia-Pacific. I co-wrote technical RFCs in English, aligned daily with international Product Owners, and supported global partners.*
>
> *In remote work, I strongly believe in what your JD says: **it is better to communicate too much than too little**. I am very proactive on Slack/Teams, I document my decisions clearly, and I never wait for a small blocker to turn into a big problem."*

---

#### ❓ Soru 11: *"Can you give me an example of a complex production problem you solved?"*
*(Çözdüğün karmaşık bir canlı ortam problemini örnek verir misin?)*

**🎤 İngilizce Cevap (Drivee Monolith → Kafka/SignalR Ayrıştırması veya Yemeksepeti Datadog/Redis Optimizasyonu):**
> *"I can share two quick examples:*
>
> *First, at **Yemeksepeti Mahalle**, during peak hours with tens of thousands of orders, we noticed latency spikes in our order and campaign endpoints. Instead of guessing, I analyzed **Datadog distributed traces**, pinpointed the exact database bottlenecks in PostgreSQL, introduced a **distributed Redis caching layer** with proper invalidation, and verified the fix with **Locust load tests up to 500,000 requests**.*
>
> *Second, at **Drivee**, heavy analytical reports on our legacy monolith were causing **thread starvation** and delaying real-time emergency alerts (like crash and cold-chain notifications for 80 KFC branches). I took ownership and decoupled the architecture using the **Outbox Pattern, Debezium CDC, and Kafka**, moving real-time notifications into an isolated **.NET 10** microservice. After that, even under maximum reporting load, critical alerts flowed in milliseconds."*

---

### E. Lojistik, Çalışma Saatleri ve Ücret (Monthly Fee) Soruları

#### ❓ Soru 12: *"What is your monthly fee expectation? (Remember: fixed monthly net pay, 20 paid holidays, 5 sick days, paid Turkish national holidays, bank transfer costs covered)"*
*(Aylık sabit net ücret beklentin nedir?)*

> [!TIP]
> **Rakam Stratejisi:**
> - Kees'in modeli: Yıllık kontrat (otomatik yenilenen), ayda sabit net Euro ödemesi (izin kullandığın veya resmi tatil olan aylarda da maaşın kesilmiyor, tam yatıyor).
> - Kendi hedeflediğin net rakama göre (örneğin **€4,500 – €5,200 EUR net/ay** veya aklındaki bant) aşağıdaki cümleyi kur ve topu Kees'e at.

**🎤 İngilizce Cevap:**
> *"Considering my 10+ years of experience in .NET, e-commerce, and high-volume integration architectures, and taking into account the fixed monthly package with paid holidays that you described, my expectation is in the range of **€[4,500] to €[5,200] net per month**.*
>
> *That being said, my main priority is a long-term, multi-year collaboration with your client, so I am open to discussing this based on the client's budget band for the role. How does that range sound from your perspective?"*

---

#### ❓ Soru 13: *"What is your availability / notice period, and are you okay with the 10:00 AM CET daily standup?"*
*(Ne zaman başlayabilirsin ve her gün 10:00 CET standup saati senin için uygun mu?)*

**🎤 İngilizce Cevap:**
> *"Regarding working hours: **10:00 AM CET** is 11:00 AM (or 12:00 PM in winter) in Istanbul, which is right in the middle of my morning routine—so it's 100% ideal for me.*
>
> *Regarding my availability: [I can start within 2 to 4 weeks / I have a standard 1-month notice period to ensure a clean handover at Drivee, though I can try to make it shorter once we finalize the contract]."*

---

## 5. Toplantı Sonunda Kees'e Soracağın Sorular

Görüşmenin sonunda Kees *"Do you have any questions?"* dediğinde şu 4 sorudan 2–3 tanesini sor:

1. **Mevcut Entegrasyon Katmanının Durumu (Senior Sahiplenme Sorusu):**
   > *"Since the main goal is to transition the platform from the external agency to the in-house team before the end of 2026, what is the current state of the .NET integration layer? Is it already event-driven with a message broker, or will the in-house team be redesigning parts of that architecture?"*
2. **En Kritik Entegrasyon Acı Noktası:**
   > *"Between BigCommerce, NetSuite (ERP), Akeneo (PIM), and Channable, which integration currently causes the most operational headaches for the client?"*
3. **2. Aşamadaki 4 Saatlik Teknik Ödev (Technical Assignment):**
   > *"I saw in the JD that the next step is a 4-hour technical assignment reviewed by the Engineering Lead. Could you tell me a bit about what they focus on most in that assignment—for example, architectural design, resilience/error handling, or clean .NET code?"*
4. **Müşteriye Sunum Süreci:**
   > *"When you present my profile to the Engineering Lead and CTO, is there any specific part of my CV or experience you think we should highlight even more?"* *(Bu soru Kees ile aranda müthiş bir takım ruhu kurar!)*

---

## 6. Görüşme Anında Açık Tutacağın "1 Bakışta Kopya Kağıdı"

> 🖥️ **Görüşme sırasında bu tabloyu ekranda açık tut; her soruda buradaki 1-2 somut kanıtı cümlene ekle!**

| Konu Sorulduğunda | Söyleyeceğin Somut CV Kanıtı (İngilizce Anahtar Kelimeler) |
|---|---|
| **E-Commerce & Orders** | **Yemeksepeti Mahalle** (Tens of thousands of daily orders, Order & Campaign microservices built from scratch with **.NET & PostgreSQL**, **500k requests** Locust load test) + **DeFacto** (Global omnichannel, marketplace, return logistics) + **MetalAVM** (Hardware e-commerce!). |
| **3rd-Party Integrations** | **Delivery Hero Tech Hub** (25-person Integration Hub, built **Sandbox Testing Platform** for **750 TR + 1,500 APAC = 2,250+ integrators**). |
| **ERP / CMS / PSP** | **Novartz** (Integrated **LOGO & E-Flow ERPs**, **Bank Payment Gateways / PSPs**, and **15+ CMS/CRM** portals) + **Halkbank** (Financial security & API Gateway). |
| **Event-Driven & Queues** | **Drivee** (**25M packets/day**, **Transactional Outbox Pattern**, **Debezium CDC**, **Kafka**, **Hangfire**) + **Yemeksepeti/DH** (**AWS SNS/SQS**, **RabbitMQ**). |
| **Architecture & .NET** | **10+ years C#**, upgraded services to **.NET 10**, **DDD, Clean Architecture, Vertical Slicing, API Gateway**, **SonarQube + GitHub Actions**. |
| **Observability** | **Datadog** (distributed tracing & latency optimization with Redis), **Elastic APM, Elasticsearch, Graylog, Health Checks**. |
| **Culture & English** | **2+ years 100% English at Delivery Hero** (Berlin/APAC) + **Erasmus+ in Krakow, Poland (AGH Univ)** + **GDPR Compliance (2024)**. |
