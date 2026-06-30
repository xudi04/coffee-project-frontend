<template>
  <footer class="app-footer">
    <div class="container">
      
      <div class="footer-top">
        
        <div class="footer-col brand-col">
          <h3 class="footer-brand">TARIK'S COFFEE ROASTERS</h3>
          <p class="footer-motto">
            Her çekirdeğin hikayesini milimetrik bir titizlikle işliyor, nitelikli kahve deneyimini kapınıza getiriyoruz.
          </p>
        </div>

        <div class="footer-col">
          <h4 class="footer-title">Keşfet</h4>
          <ul class="footer-links">
            <li v-for="(link, index) in discoverLinks" :key="'discover-' + index">
              <a :href="link.url">{{ link.name }}</a>
            </li>
          </ul>
        </div>

        <div class="footer-col">
          <h4 class="footer-title">Alışveriş</h4>
          <ul class="footer-links">
            <li v-for="(link, index) in shopLinks" :key="'shop-' + index">
              <a :href="link.url">{{ link.name }}</a>
            </li>
          </ul>
        </div>

        <div class="footer-col contact-col">
          <h4 class="footer-title">İletişim</h4>
          <a href="mailto:info@tarikscoffee.com" class="footer-email">info@tarikscoffee.com</a>
          
          <div class="social-icons">
            <a 
              v-for="social in socialMedia" 
              :key="social.name"
              :href="social.url"
              target="_blank"
              rel="noopener noreferrer"
              :aria-label="social.name"
              v-html="social.iconSvg"
            >
            </a>
          </div>
        </div>

      </div>

      <div class="footer-bottom">
        <p class="copyright">© 2026 Tarık's Coffee Roasters. Tüm Hakları Saklıdır.</p>
        <p class="designer">Milimetrik Tasarım & Geliştirme MK designer.</p>
      </div>

    </div>
  </footer>
</template>

<script setup>
import { ref } from 'vue'

// --- 1. DINAMIK LINK VE ROUTER HAZIRLIK DIZILERI ---
const discoverLinks = ref([
  { name: 'Ana Sayfa', url: '#hero' },
  { name: 'Hikayemiz', url: '#about' },
  { name: 'Kahvelerimiz', url: '#coffees' },
  { name: 'Şubelerimiz', url: '#branches' }
])

const shopLinks = ref([
  { name: 'Single Origin', url: '#single-origin' },
  { name: 'House Blend', url: '#house-blend' },
  { name: 'Filtre Kahve', url: '#filter-coffee' },
  { name: 'Ekipmanlar', url: '#equipments' }
])

// Güvenli harici yönlendirmeler için Sosyal Medya Obje Dizisi
const socialMedia = ref([
  {
    name: 'Instagram',
    url: 'https://instagram.com',
    iconSvg: `<svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><rect x="2" y="2" width="20" height="20" rx="5" ry="5"></rect><path d="M16 11.37A4 4 0 1 1 12.63 8 4 4 0 0 1 16 11.37z"></path><line x1="17.5" y1="6.5" x2="17.51" y2="6.5"></line></svg>`
  },
  {
    name: 'YouTube',
    url: 'https://youtube.com',
    iconSvg: `<svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M22.54 6.42a2.78 2.78 0 0 0-1.94-2C18.88 4 12 4 12 4s-6.88 0-8.6.46a2.78 2.78 0 0 0-1.94 2A29 29 0 0 0 1 11.75a29 29 0 0 0 .46 5.33A2.78 2.78 0 0 0 3.4 19c1.72.46 8.6.46 8.6.46s6.88 0 8.6-.46a2.78 2.78 0 0 0 1.94-2 29 29 0 0 0 .46-5.25 29 29 0 0 0-.46-5.33z"></path><polygon points="9.75 15.02 15.5 11.75 9.75 8.48 9.75 15.02"></polygon></svg>`
  },
  {
    name: 'WhatsApp',
    url: 'https://wa.me',
    iconSvg: `<svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 11.5a8.38 8.38 0 0 1-.9 3.8 8.5 8.5 0 0 1-7.6 4.7 8.38 8.38 0 0 1-3.8-.9L3 21l1.9-5.7a8.38 8.38 0 0 1-.9-3.8 8.5 8.5 0 0 1 4.7-7.6 8.38 8.38 0 0 1 3.8-.9h.5a8.48 8.48 0 0 1 8 8v.5z"></path></svg>`
  }
])
</script>

<style scoped>
/* --- 4. GÖRSEL NİZAM VE MİKRO ETKİLEŞİM CSS KANUNLARI --- */
.app-footer {
  width: 100%;
  background-color: var(--bg-antrasit); /* Asil mat antrasit zemin */
  padding: 80px 0 40px 0;
  color: #ffffff;
}

/* Sütunların 4'lü Grid Hizalaması */
.footer-top {
  display: grid;
  grid-template-columns: 1.5fr 1fr 1fr 1.2fr;
  gap: 48px;
  padding-bottom: 60px;
}

.footer-col {
  display: flex;
  flex-direction: column;
}

/* Marka ve Motto */
.footer-brand {
  font-family: var(--font-sans);
  font-size: 1.15rem;
  font-weight: 600;
  letter-spacing: 0.05em;
  color: #ffffff;
  margin-bottom: 24px;
}

.footer-motto {
  font-family: var(--font-sans);
  font-size: 0.9rem;
  line-height: 1.7;
  color: #8c8d8e;
  max-width: 320px;
}

/* Sütun Başlıkları */
.footer-title {
  font-family: var(--font-sans);
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--color-gold);
  margin-bottom: 24px;
}

/* Link Grupları */
.footer-links {
  list-style: none;
  display: flex;
  flex-direction: column;
  gap: 14px;
}

/* Menü Linkleri Pürüzsüz Hover Efektleri */
.footer-links a {
  font-family: var(--font-sans);
  font-size: 0.95rem;
  color: #a3a4a5;
  text-decoration: none;
  /* İstediğin yumuşatılmış geçiş süresi */
  transition: color 0.3s ease, padding-left 0.2s ease;
}

.footer-links a:hover {
  color: var(--color-gold); /* Gold rengine pürüzsüz geçiş */
  padding-left: 4px; /* Soluk interaktif esneme */
}

/* İletişim ve Mail Alanı */
.footer-email {
  font-family: var(--font-sans);
  font-size: 1.05rem;
  font-weight: 600;
  color: #ffffff;
  text-decoration: none;
  margin-bottom: 20px;
  transition: color 0.3s ease;
}

.footer-email:hover {
  color: var(--color-gold);
}

/* Sosyal Medya İkon Grubu ve Esneme Lojiği */
.social-icons {
  display: flex;
  gap: 24px;
  align-items: center;
}

.social-icons a {
  color: #ffffff;
  display: inline-block;
  /* İstediğin pürüzsüz ivmeli transition ayarı */
  transition: color 0.3s ease, transform 0.2s ease;
}

.social-icons a:hover {
  color: var(--color-gold); /* Gold rengine dönüşüm */
  transform: translateY(-2px); /* İstediğin hafifçe yukarı doğru esneme */
}

/* --- Alttaki İnce Beyaz Ayırıcı Çizgi ve Telif Alanı --- */
.footer-bottom {
  border-top: 1px solid rgba(255, 255, 255, 0.1); /* %10 Opaklıkta ince sınır çizgisi */
  padding-top: 32px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.copyright, .designer {
  font-family: var(--font-sans);
  font-size: 0.9rem;
  font-weight: 600;
  color: #ffffff;
}

/* ==========================================================================
   RESPONSIVE DESIGN (Bütünlüğü Koruyan Kırılımlar)
   ========================================================================== */

@media (max-width: 992px) {
  .footer-top {
    grid-template-columns: repeat(2, 1fr);
    gap: 40px;
  }
}

@media (max-width: 600px) {
  .app-footer {
    padding: 60px 0 30px 0;
  }

  .footer-top {
    grid-template-columns: 1fr; /* Mobilde tek kolon tam akış */
    gap: 32px;
    padding-bottom: 40px;
  }

  .footer-brand, .footer-title {
    margin-bottom: 16px;
  }

  .footer-bottom {
    flex-direction: column;
    text-align: center;
    gap: 12px;
  }
}
</style>