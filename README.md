new Swiper(".holidaySwiper", {
  loop: true,
  loopAdditionalSlides: 5,   // 🔥 Fixes warning
  centeredSlides: true,
  slidesPerView: 1.4,
  spaceBetween: 30,
  speed: 900,
  autoplay: {
    delay: 3500,
    disableOnInteraction: false,
  },
  pagination: {
    el: ".swiper-pagination",
    clickable: true,
  }
});




0:380 Swiper Loop Warning: The number of slides is not enough for loop mode, it will be disabled or not function properly. You need to add more slides (or make duplicates) or lower the values of slidesPerView and slidesPerGroup parameters

application name 

TSUISL Executive Holiday Plan
