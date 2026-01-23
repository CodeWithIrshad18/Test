body {
  margin: 0;
  font-family: 'Poppins', sans-serif;
  background: radial-gradient(
    circle at top,
    #1a1a1a 0%,
    #0d0d0d 40%,
    #000000 100%
  );
  color: #fff;
  overflow-x: hidden;
}

.app-header {
  text-align: center;
  padding: 28px 20px 18px;
  margin: 20px auto;
  width: fit-content;
  backdrop-filter: blur(14px);
  background: rgba(255, 255, 255, 0.08);
  border-radius: 20px;
  box-shadow: 0 20px 40px rgba(0,0,0,0.45);
}

.app-header h1 {
  font-size: 2.9rem;
  letter-spacing: 4px;
  font-weight: 700;
  margin: 0;
  background: linear-gradient(135deg, #f7d046, #f5b942);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.app-header p {
  font-size: 0.9rem;
  margin-top: 6px;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: rgba(255,255,255,0.75);
}


.swiper {
  filter: drop-shadow(0 30px 60px rgba(0,0,0,0.6));
}

.swiper-slide-active::after {
  content: "";
  position: absolute;
  inset: 0;
  border-radius: 28px;
  box-shadow: inset 0 0 0 1px rgba(255,255,255,0.08);
  pointer-events: none;
}


.overlay button {
  background: linear-gradient(135deg, #f7d046, #d4af37);
  color: #000;
  font-weight: 600;
  letter-spacing: 1px;
  box-shadow: 0 10px 25px rgba(212,175,55,0.4);
}

.overlay button:hover {
  transform: translateY(-2px) scale(1.05);
}



<style>
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

.swiper-slide {
  cursor: grab;
}

.swiper-slide:active {
  cursor: grabbing;
}
    </style>

    <div class="app-header">
  <h1>TSUISL</h1>
  <p>Executive Holiday Plan</p>
</div>

<div class="swiper holidaySwiper">
  <div class="swiper-wrapper">

       <div class="swiper-slide" style="background-image:url('images/kashmir.jpg')">
      <div class="overlay">
        <h2>Kashmir</h2>
        <p>Backwaters & Nature</p>
        <button>Explore Now</button>
      </div>
    </div>


    <div class="swiper-slide" style="background-image:url('images/Puri.jpg')">
      <div class="overlay">
        <h2>Puri</h2>
        <p>Sun • Beach • Party</p>
        <button>Explore</button>
      </div>
    </div>

   

    <div class="swiper-slide" style="background-image:url('images/kolkata3.jpg')">
      <div class="overlay">
        <h2>Kolkata</h2>
        <p>City of Joy</p>
        <button>Explore</button>
      </div>
    </div>

    <div class="swiper-slide" style="background-image:url('images/sikkim.jpg')">
      <div class="overlay">
        <h2>Sikkim</h2>
        <p>Backwaters & Nature</p>
        <button>Explore</button>
      </div>
    </div>
     <div class="swiper-slide" style="background-image:url('images/rajasthan5.jpg')">
      <div class="overlay">
        <h2>Rajasthan</h2>
        <p>Backwaters & Nature</p>
        <button>Explore Now</button>
      </div>
    </div>
     <div class="swiper-slide" style="background-image:url('images/Andaman.jpg')">
      <div class="overlay">
        <h2>Andaman</h2>
        <p>Backwaters & Nature</p>
        <button>Explore Now</button>
      </div>
    </div>
     <div class="swiper-slide" style="background-image:url('images/Darjeeling.jpg')">
      <div class="overlay">
        <h2>Darjeeling</h2>
        <p>Backwaters & Nature</p>
        <button>Explore Now</button>
      </div>
    </div>
     <div class="swiper-slide" style="background-image:url('images/rishikesh.jpg')">
      <div class="overlay">
        <h2>Rishikesh</h2>
        <p>Backwaters & Nature</p>
        <button>Explore Now</button>
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
  spaceBetween: -120,   
  speed: 1000,
  grabCursor: true,        
  resistanceRatio: 0.5,
  autoplay: {
    delay: 1500,
    disableOnInteraction: false,
  },
  pagination: {
    el: ".swiper-pagination",
    clickable: true,
  }
});
</script>
