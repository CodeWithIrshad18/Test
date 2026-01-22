<div class="app-header">
  <h1>TSUISL</h1>
  <p>Executive Holiday Plan</p>
</div>

.app-header {
  text-align: center;
  padding: 30px 0 10px;
}

.app-header h1 {
  font-size: 2.8rem;
  letter-spacing: 3px;
  font-weight: 600;
  margin: 0;
}

.app-header p {
  font-size: 1rem;
  opacity: 0.7;
  letter-spacing: 2px;
  text-transform: uppercase;
}
new Swiper(".holidaySwiper", {
  loop: true,
  centeredSlides: true,
  slidesPerView: 1.4,
  spaceBetween: -120,
  speed: 900,
  grabCursor: true,        // 👈 shows grab cursor
  resistanceRatio: 0.5,   // 👈 smooth drag
  autoplay: {
    delay: 3500,
    disableOnInteraction: false,
  },
  pagination: {
    el: ".swiper-pagination",
    clickable: true,
  },
});

.swiper-slide {
  cursor: grab;
}

.swiper-slide:active {
  cursor: grabbing;
}



body {
  background: #f5f5f5;
  font-family: 'Poppins', sans-serif;
}

.swiper {
  width: 100%;
  height: 75vh;
  padding: 60px 0;
}

.swiper-slide {
  width: 65%;
  background-size: cover;
  background-position: center;
  border-radius: 28px;
  overflow: hidden;
  position: relative;
  transition: all 0.6s ease;
  transform: scale(0.85);
  opacity: 0.6;
  filter: blur(1.5px);
  box-shadow: 0 25px 50px rgba(0,0,0,0.3);
}

.swiper-slide-active {
  transform: scale(1);
  opacity: 1;
  filter: blur(0);
  z-index: 10;
}

.swiper-slide-prev,
.swiper-slide-next {
  transform: scale(0.9);
  opacity: 0.8;
  filter: blur(0.5px);
  z-index: 5;
}

.swiper-slide::before {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgba(0,0,0,0.65), rgba(0,0,0,0.1));
  z-index: 1;
}

.overlay {
  position: absolute;
  bottom: 12%;
  left: 8%;
  z-index: 2;
  color: #fff;
}

.overlay h2 {
  font-size: 2.6rem;
  font-weight: 600;
  letter-spacing: 1px;
}

.overlay p {
  font-size: 1.1rem;
  opacity: 0.9;
}

.overlay button {
  margin-top: 14px;
  padding: 12px 28px;
  border-radius: 30px;
  border: none;
  background: linear-gradient(135deg, #ff7a18, #ffb347);
  color: #fff;
  font-size: 14px;
  cursor: pointer;
  transition: 0.3s ease;
}

.overlay button:hover {
  transform: scale(1.05);
}

.swiper-pagination-bullet {
  background: white;
  opacity: 0.4;
}

.swiper-pagination-bullet-active {
  opacity: 1;
}


<div class="swiper holidaySwiper">
  <div class="swiper-wrapper">

    <div class="swiper-slide" style="background-image:url('images/Puri.jpg')">
      <div class="overlay">
        <h2>Puri</h2>
        <p>Sun • Beach • Party</p>
        <button>Explore</button>
      </div>
    </div>

    <div class="swiper-slide" style="background-image:url('images/kashmir.jpg')">
      <div class="overlay">
        <h2>Kashmir</h2>
        <p>Heaven on Earth</p>
        <button>Explore</button>
      </div>
    </div>

    <div class="swiper-slide" style="background-image:url('images/kerala.jpg')">
      <div class="overlay">
        <h2>Kerala</h2>
        <p>Backwaters & Nature</p>
        <button>Explore</button>
      </div>
    </div>

  </div>

  <div class="swiper-pagination"></div>
</div>


<script src="https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.js"></script>

<script>
new Swiper(".holidaySwiper", {
  loop: true,
  centeredSlides: true,
  slidesPerView: 1.4,
  spaceBetween: -120,   // 👈 Overlapping like reference
  speed: 1000,
  autoplay: {
    delay: 3500,
    disableOnInteraction: false,
  },
  pagination: {
    el: ".swiper-pagination",
    clickable: true,
  }
});
</script>



0:380 Swiper Loop Warning: The number of slides is not enough for loop mode, it will be disabled or not function properly. You need to add more slides (or make duplicates) or lower the values of slidesPerView and slidesPerGroup parameters
