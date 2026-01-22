<!-- Swiper -->
<div class="swiper holidaySwiper">
  <div class="swiper-wrapper">

    <div class="swiper-slide" style="background-image:url('images/goa.jpg')">
      <div class="overlay">
        <h2>Goa</h2>
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

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.css" />
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



SELECT
    p.TRBDGDA_BD_PNO AS PNo,
    e.ema_dept_desc,

    /* MOBILE punches only */
    SUM(CASE 
            WHEN p.TRBDGDA_BD_ENTRYUID = 'MOBILE'
             AND p.TRBDGDA_BD_DATE >= '2025-11-01'
             AND p.TRBDGDA_BD_DATE <  '2025-12-01'
            THEN 1 ELSE 0 
        END) AS November_Punches,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_ENTRYUID = 'MOBILE'
             AND p.TRBDGDA_BD_DATE >= '2025-12-01'
             AND p.TRBDGDA_BD_DATE <  '2026-01-01'
            THEN 1 ELSE 0 
        END) AS December_Punches,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_ENTRYUID = 'MOBILE'
             AND p.TRBDGDA_BD_DATE >= '2026-01-01'
             AND p.TRBDGDA_BD_DATE <  '2026-02-01'
            THEN 1 ELSE 0 
        END) AS January_Punches,

    /* TOTAL punches (ALL entry sources) */
    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2025-11-01'
             AND p.TRBDGDA_BD_DATE <  '2025-12-01'
            THEN 1 ELSE 0 
        END) AS November_Total_Punches,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2025-12-01'
             AND p.TRBDGDA_BD_DATE <  '2026-01-01'
            THEN 1 ELSE 0 
        END) AS December_Total_Punches,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2026-01-01'
             AND p.TRBDGDA_BD_DATE <  '2026-02-01'
            THEN 1 ELSE 0 
        END) AS January_Total_Punches

FROM T_TRBDGDAT_EARS p
INNER JOIN SAPHRDB.dbo.T_EMPL_ALL e
    ON e.ema_perno = p.TRBDGDA_BD_PNO
WHERE e.ema_pyrl_area IN ('JS', 'ZZ')
  AND p.TRBDGDA_BD_DATE >= '2025-11-01'
  AND p.TRBDGDA_BD_DATE <  '2026-02-01'

GROUP BY
    p.TRBDGDA_BD_PNO,
    e.ema_dept_desc
ORDER BY
    e.ema_dept_desc;







SELECT
    p.TRBDGDA_BD_PNO as PNo,e.ema_dept_desc,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2025-11-01'
             AND p.TRBDGDA_BD_DATE <  '2025-12-01'
            THEN 1 ELSE 0 
        END) AS November_Punches,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2025-12-01'
             AND p.TRBDGDA_BD_DATE <  '2026-01-01'
            THEN 1 ELSE 0 
        END) AS December_Punches,

    SUM(CASE 
            WHEN p.TRBDGDA_BD_DATE >= '2026-01-01'
             AND p.TRBDGDA_BD_DATE <  '2026-02-01'
            THEN 1 ELSE 0 
        END) AS January_Punches

FROM T_TRBDGDAT_EARS p
INNER JOIN SAPHRDB.dbo.T_EMPL_ALL e
    ON e.ema_perno = p.TRBDGDA_BD_PNO
WHERE p.TRBDGDA_BD_ENTRYUID = 'MOBILE'
  AND e.ema_pyrl_area IN ('JS', 'ZZ')
  AND p.TRBDGDA_BD_DATE >= '2025-11-01'
  AND p.TRBDGDA_BD_DATE <  '2026-02-01'

GROUP BY p.TRBDGDA_BD_PNO,e.ema_dept_desc
ORDER BY e.ema_dept_desc;
