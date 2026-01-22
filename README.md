<style>
    .swiper {
  width: 100%;
  height: 75vh;
  border-radius: 16px;
  overflow: hidden;
}

.swiper-slide {
  background-size: cover;
  background-position: center;
  position: relative;
}

.overlay {
  position: absolute;
  bottom: 15%;
  left: 6%;
  color: #fff;
  max-width: 70%;
}

.overlay h2 {
  font-size: 2.2rem;
  margin-bottom: 5px;
}

.overlay p {
  font-size: 1.1rem;
  opacity: 0.9;
}

.overlay button {
  margin-top: 12px;
  padding: 10px 22px;
  border: none;
  border-radius: 25px;
  background: #ff6b35;
  color: white;
  font-size: 14px;
  cursor: pointer;
}

.swiper-slide::before {
  content: "";
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgba(0,0,0,.6), transparent);
}

.swiper-pagination-bullet {
  background: #fff;
  opacity: 0.6;
}

.swiper-pagination-bullet-active {
  opacity: 1;
}

    </style>


<!-- Swiper -->
<div class="swiper holidaySwiper">
  <div class="swiper-wrapper">

    <div class="swiper-slide" style="background-image:url('images/Puri.jpg')">
      <div class="overlay">
        <h2>Puri</h2>
        <p>Sun • Beach • Party</p>
        <button>Explore Now</button>
      </div>
    </div>

    <div class="swiper-slide" style="background-image:url('images/kashmir.jpg')">
      <div class="overlay">
        <h2>Kashmir</h2>
        <p>Heaven on Earth</p>
        <button>Explore Now</button>
      </div>
    </div>

    <div class="swiper-slide" style="background-image:url('images/kerala.jpg')">
      <div class="overlay">
        <h2>Kerala</h2>
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
    speed: 900,
    autoplay: {
      delay: 4000,
      disableOnInteraction: false,
    },
    pagination: {
      el: ".swiper-pagination",
      clickable: true,
    },
    effect: "slide",
  });
</script>
