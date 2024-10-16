<template>
  <div class="flex justify-center items-center min-h-[63px] w-full max-w-[480px] pb-2 mx-auto relative">
    <div class="absolute w-full h-full pt-5">
      <div class="flex justify-between items-start w-full mb-1">
      <!-- <svg xmlns="http://www.w3.org/2000/svg" class="icon" fill="none" viewBox="0 0 24 24" stroke="currentColor">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4.5 12.75l7.5 7.5m0 0l7.5-7.5m-7.5 7.5V3" />
    </svg> -->
    <div class="pt-2 pb-2 pl-1">
       <svg version="1.0" xmlns="http://www.w3.org/2000/svg"
 width="512.000000pt" height="512.000000pt" viewBox="0 0 512.000000 512.000000"
 preserveAspectRatio="xMidYMid meet" class="h-5 w-5" @click="showHowToPlay">

<g transform="translate(0.000000,512.000000) scale(0.100000,-0.100000)"
fill="#6b7280" stroke="none">
<path d="M2330 5110 c-494 -48 -950 -230 -1350 -538 -195 -150 -448 -432 -594
-662 -63 -99 -186 -351 -230 -471 -49 -134 -102 -340 -128 -499 -31 -195 -31
-565 0 -760 45 -276 116 -498 237 -745 132 -269 269 -460 489 -681 221 -220
412 -357 681 -489 247 -121 469 -192 745 -237 195 -31 565 -31 760 0 276 45
498 116 745 237 269 132 460 269 681 489 220 221 357 412 489 681 88 179 132
296 180 476 63 240 78 371 78 649 0 278 -15 409 -78 649 -48 180 -92 297 -180
476 -132 269 -269 460 -489 681 -221 220 -412 357 -681 489 -246 121 -474 193
-740 235 -147 23 -475 34 -615 20z m426 -431 c503 -49 947 -259 1305 -618 361
-362 569 -802 618 -1305 51 -522 -97 -1041 -422 -1476 -92 -123 -296 -328
-417 -418 -438 -325 -954 -472 -1476 -421 -503 49 -947 259 -1305 618 -361
362 -569 802 -618 1305 -51 522 97 1041 422 1476 92 123 296 328 417 418 438
324 955 472 1476 421z"/>
<path d="M2410 4145 c-104 -18 -172 -41 -260 -85 -310 -156 -505 -461 -513
-799 -1 -66 3 -86 24 -127 54 -105 174 -143 283 -90 79 39 105 83 122 210 20
153 55 234 140 321 195 201 514 200 709 0 179 -185 184 -479 11 -672 -87 -97
-195 -149 -339 -164 -131 -13 -197 -56 -229 -146 -15 -42 -19 -549 -5 -614 10
-47 71 -117 121 -140 52 -24 120 -24 171 0 43 19 100 77 117 120 6 14 14 105
17 201 l6 174 40 12 c316 92 570 368 641 697 19 87 21 267 4 357 -91 492 -573
830 -1060 745z"/>
<path d="M2453 1584 c-23 -9 -62 -33 -88 -56 -164 -144 -128 -402 70 -498 71
-35 179 -35 250 0 198 96 234 354 70 498 -25 22 -67 48 -92 56 -57 20 -154 19
-210 0z"/>
</g>
      </svg>
    </div>
   
    

    <div class="flex justify-center items-center pb-2">
      <svg version="1.0" xmlns="http://www.w3.org/2000/svg"
 width="512.000000pt" height="512.000000pt" viewBox="0 0 512.000000 512.000000"
 preserveAspectRatio="xMidYMid meet" class="h-5 w-5" @click="showStatistic" >

<g transform="translate(0.000000,512.000000) scale(0.100000,-0.100000)"
fill="#6b7280" stroke="none">
<path d="M1695 5101 c-51 -23 -101 -79 -114 -128 -7 -25 -11 -278 -11 -730 l0
-693 -692 0 c-458 0 -706 -4 -731 -11 -21 -6 -56 -26 -77 -45 -76 -69 -71 69
-68 -1741 l3 -1621 30 -43 c17 -23 48 -52 70 -63 l40 -21 2415 0 2415 0 40 21
c22 11 54 40 70 63 l30 43 0 1248 0 1248 -30 43 c-16 23 -50 53 -74 66 l-44
23 -708 0 -709 0 -2 1108 c-3 1221 2 1131 -65 1194 -63 60 -25 58 -925 58
-774 0 -825 -2 -863 -19z m1455 -2541 l0 -2160 -590 0 -590 0 0 2160 0 2160
590 0 590 0 0 -2160z m-1580 -785 l0 -1375 -585 0 -585 0 0 1375 0 1375 585 0
585 0 0 -1375z m3150 -395 l0 -980 -585 0 -585 0 0 980 0 980 585 0 585 0 0
-980z"/>
</g>
</svg>
      <svg version="1.0" xmlns="http://www.w3.org/2000/svg"
 width="512.000000pt" height="512.000000pt" viewBox="0 0 512.000000 512.000000"
 preserveAspectRatio="xMidYMid meet" class="h-7 w-7 ml-3">

<g transform="translate(0.000000,512.000000) scale(0.100000,-0.100000)"
fill="#6b7280" stroke="none">
<path d="M2285 4675 c-708 -93 -1326 -538 -1640 -1180 -289 -592 -289 -1278 0
-1870 323 -661 956 -1104 1694 -1185 170 -19 442 -8 614 24 755 143 1380 685
1626 1413 149 439 149 927 0 1366 -265 782 -964 1345 -1784 1437 -122 13 -387
11 -510 -5z m414 -415 c843 -75 1493 -730 1561 -1575 50 -606 -252 -1217 -765
-1551 -579 -376 -1314 -372 -1885 12 -576 386 -861 1085 -720 1761 138 662
662 1185 1326 1324 170 35 313 44 483 29z"/>
<path d="M2510 3621 c-74 -24 -119 -63 -145 -123 -23 -56 -23 -1180 0 -1236
64 -152 273 -172 370 -34 l30 44 3 586 c2 399 0 599 -8 624 -28 97 -158 169
-250 139z"/>
<path d="M2486 1905 c-80 -28 -136 -110 -136 -200 0 -122 86 -205 213 -205
110 0 196 77 205 185 13 160 -132 274 -282 220z"/>
</g>
        </svg>
    </div>
    </div>
    <div class="line "></div>
    </div>
    <div class="flex justify-center items-center ">
      <div class="title"> <a class=" text-red-600">K</a>AT<a class=" text-green">LA</a></div>
      <div class=" font-bold self-start pt-2">#{{ numberOfDays }}</div>
    </div>
  </div>
  
</template>

<script>
export default {
  props: ['numberOfDays'],
  name: 'HeaderComp',
  methods: {
    showHowToPlay(){
      this.$emit('showHowToPlay');
    },
    showStatistic(){
      this.$emit('showStatistic');
    }
  }
}
</script>

<style scoped>
/* .header {
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
} */
.icon {
  width: 24px;
  height: 24px;
  margin-right: 8px;
}
.title {
  font-size: 2.2rem;
  font-weight: 700;
  letter-spacing: 5px;
}
.line {
  /* border-color: #6b7280; */
  border-top: 1px solid #6b7280;
}
.text-green {
  color: #009736;
}
</style>
