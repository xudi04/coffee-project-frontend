<template>
  <section class="story-section">
    <div id="about" class="container story-container">
      
      <div class="story-image-wrapper">
        <img 
          src="https://images.unsplash.com/photo-1514432324607-a09d9b4aefdd?q=80&w=800&auto=format&fit=crop" 
          alt="Kahve Kavurma Atölyemiz" 
          class="story-img"
        />
      </div>

      <div class="story-content-wrapper">
        <p class="story-text">
          Biz kahveyi sadece kavurmuyoruz; her çekirdeğin karakterini, asiditesini ve gövdesini milimetrik bir titizlikle ortaya çıkarıyoruz.
        </p>
        
        <div class="story-signature">
          Tarık
        </div>
      </div>

    </div>
  </section>
</template>

<script setup>
</script>

<style scoped>
.story-section {
  width: 100%;
  background-color: var(--bg-espresso); /* Tasarımdaki koyu espresso tonu */
  padding: 120px 0;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}

.story-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 80px; /* Görsel ile metin arasındaki ideal nefes alma mesafesi */
}

/* --- GÖRSEL ALANI --- */
.story-image-wrapper {
  flex: 1;
  max-width: 440px; /* Masaüstü için ideal görsel en boy dengesi */
  aspect-ratio: 4 / 5; /* Tasarımdaki dikey dikdörtgen form */
  overflow: hidden;
  box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
}

.story-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  filter: brightness(0.85); /* Tasarımdaki loş tona sadakat */
  transition: var(--transition-smooth);
}

.story-img:hover {
  transform: scale(1.03);
  filter: brightness(0.95);
}

/* --- METİN ALANI --- */
.story-content-wrapper {
  flex: 1.2;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
  min-height: 350px;
}

.story-text {
  font-family: var(--font-sans);
  font-size: 1.5rem;
  font-weight: 300;
  color: var(--bg-cream);
  line-height: 2.2; /* Tasarımdaki satır arası boşluk hissi */
  text-align: center;
  letter-spacing: 0.12em; /* Metnin yayılımındaki o özel mikro-boşluklar */
  margin-bottom: 40px;
}

/* --- İMZA ALANI --- */
.story-signature {
  align-self: flex-end; /* İçeriği sağ alta yaslar */
  margin-right: 10%; /* Tam köşeye yapışmaması için tasarıma uygun pay */
  font-family: 'Great Vibes', 'Alex Brush', 'Dancing Script', cursive; /* Premium el yazısı fontları */
  font-size: 2.5rem;
  color: rgba(251, 249, 246, 0.85); /* Hafif şeffaf krem rengi */
  letter-spacing: 0.05em;
}

/* ==========================================================================
   RESPONSIVE DESIGN (İstediğiniz Kırılımlara Milimetrik Uyum)
   ========================================================================== */

/* 1. Kural: max-width 800px ise yazı ve image boyutunu küçült */
@media (max-width: 800px) {
  .story-container {
    gap: 40px; /* Boşluğu daraltıyoruz */
  }

  .story-image-wrapper {
    max-width: 320px; /* Görsel boyutu küçültüldü */
  }

  .story-text {
    font-size: 1.15rem; /* Yazı boyutu küçültüldü */
    line-height: 2;
    letter-spacing: 0.08em;
  }

  .story-signature {
    font-size: 2rem;
    margin-right: 5%;
  }
}

/* 2. Kural: max-width 500px ise resim üstte, yazı altta olacak */
@media (max-width: 500px) {
  .story-section {
    padding: 80px 0;
  }

  .story-container {
    flex-direction: column; /* Üst üste dizilme sağlandı */
    gap: 48px;
  }

  .story-image-wrapper {
    max-width: 100%;
    width: 100%; /* Resim mobil ekrana tam yayılır */
    aspect-ratio: 4 / 3; /* Mobilde daha şık durması için yatay-dikey dengesi */
  }

  .story-content-wrapper {
    width: 100%;
    min-height: auto;
  }

  .story-text {
    font-size: 1.1rem;
    line-height: 1.8;
    letter-spacing: 0.06em;
    margin-bottom: 24px;
  }

  .story-signature {
    font-size: 1.8rem;
    margin-right: 0; /* Mobilde ortalanmış metnin sağ altına tam dengeli otursun */
  }
}
</style>