### <a id="the-tldr" href="#the-tldr">너무 길어서 안 읽으신 분들을 위한 요약</a>

**이미지 압축은 전부 자동화되어야 합니다.**

2017년 안에 이미지 최적화 과정은 자동화가 되어야 합니다. 이미지 최적화를 일일이 매번 손수 한다면 실수로 무언가 빼먹기 쉽고, 그 와중에 모범 기법은 자다 일어나면 바뀌고 있으며, 빌드 파이프라인에 미처 넣지 못한 콘텐츠들은 새어나가기 마련입니다. 최적화 과정을 자동화하려면 빌드 할 때 [imagemin](https://github.com/imagemin/imagemin)이나 [libvips](https://github.com/jcupitt/libvips)를 사용해 보세요. 다른 방법들도 많이 있습니다.

대부분의 CDN(예: [Akamai](https://www.akamai.com/us/en/solutions/why-akamai/image-management.jsp))과 서드파티 솔루션(예: [Cloudinary](https://cloudinary.com), [imgix](https://imgix.com), [Fastly’s Image Optimizer](https://www.fastly.com/io/), [Instart Logic’s SmartVision](https://www.instartlogic.com/technology/machine-learning/smartvision), [ImageOptim API](https://imageoptim.com/api))에는 이미지 최적화 자동화 종합 솔루션이 포함되어 있습니다.

여러분이 직접 이미지 최적화와 관련된 블로그 포스트를 읽고, 프로젝트 설정을 만지는 데 들이게 될 시간을 비용으로 따져보면 네트워크 서비스 월 사용료를 초과합니다. (Cloudinary에는 [무료](http://cloudinary.com/pricing) 서비스 티어도 있습니다.) 비용이나 지연(latency) 문제 때문에 최적화 작업을 아웃소싱 하고 싶지 않다면, 오픈 소스 프로젝트를 선택해 보는 것도 괜찮습니다. [Imageflow](https://github.com/imazen/imageflow)나 [Thumbor](https://github.com/thumbor/thumbor)와 같은 프로젝트 소스를 사용하면 이미지 호스팅을 직접 할 수 있습니다.

**우리 모두 이미지를 효율적인 방법으로 압축해야 합니다.**

최소한 [ImageOptim](https://imageoptim.com/)이라도 사용해봅시다. 이미지 품질은 그대로 유지하면서 파일 크기를 상당히 줄일 수 있습니다. 윈도우와 리눅스 사용자를 위한 [옵션](https://imageoptim.com/versions.html)도 존재합니다.

조금 더 구체적인 방법을 얘기해 볼게요. [MozJPEG](https://github.com/mozilla/mozjpeg)을 사용해 JPEG 이미지를 압축해보고(웹 콘텐츠용 이미지는 `q=80` 이하 품질이 적당합니다.), [Progressive JPEG](http://cloudinary.com/blog/progressive_jpegs_and_green_martians)로 인코딩하는 것을 고려해 보세요. PNG는 [pngquant](https://pngquant.org/), SVG는 [SVGO](https://github.com/svg/svgo)를 사용해 압축해 보세요. 파일 크기가 불어나는 것을 방지하기 위해, 메타데이터를 확실히 지워두세요. (pngguant에서는 `--strip` 옵션을 주면 됩니다.) 미치도록 용량이 큰 애니메이션 GIF를 사용하는 대신에, [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) 비디오 (혹은 크롬, 파이어폭스, 오페라 브라우저를 지원할 예정이라면 [WebM](https://www.webmproject.org/)) 포맷을 사용해 보세요. 그럴 상황이 아니라면, 최소한 [Giflossy](https://github.com/pornel/giflossy) 정도는 사용해 보시기 바랍니다. 만약 CPU 사이클에 여유가 있고, 보통의 웹 콘텐츠보다 더 품질 좋은 이미지를 원하며, 인코딩 시간이 더 걸려도 괜찮다면 [Guetzli](https://research.googleblog.com/2017/03/announcing-guetzli-new-open-source-jpeg.html)를 사용해 보세요.

일부 브라우저에서는 Accept 요청 헤더를 통한 이미지 포맷 지원 기능을 선전하고 있습니다. 이를 활용하면 이미지 포맷을 선택적으로 사용자에게 전달할 수 있습니다. 예를 들어, 크롬과 같이 블링크 엔진을 기반으로 하는 브라우저에는 손실 [WebP](https://developers.google.com/speed/webp/) 포맷 이미지를 전달하고, 다른 브라우저에는 JPEG/PNG 포맷을 전달하는 식입니다.

최적화 작업에 끝이란 없습니다. `srcset` 분기점을 생성하고 전달하기 위한 용도로 만들어진 툴도 존재합니다. [client-hints](https://developers.google.com/web/updates/2015/09/automating-resource-selection-with-client-hints)를 사용하면 블링크 기반 브라우저가 리소스 선택을 알아서 하게 할 수 있습니다. [Save-Data](https://developers.google.com/web/updates/2016/02/save-data) 힌트를 활용하면 브라우저 설정의 "데이터 절약(data savings)" 옵션을 선택한 사용자들의 수신 데이터양을 줄일 수도 있습니다.

이미지 파일 크기를 줄이면 줄일수록 사용자(그중에도 특히 모바일 기기 사용자)에게 더 나은 네트워크 경험을 제공할 수 있습니다. 품질에 영향을 최소한으로 주는 최신 압축 기법들을 활용해 이미지 파일 크기 축소 방법을 이제 알아보도록 하겠습니다.

<details>
<summary><h2>목차</h2></summary>
<p>
<ul>
        <li><a href="#introduction">서론</a></li>
        <li><a href="#do-my-images-need-optimization">이미지 최적화가 필요한지 어떻게 알 수 있을까요?</a></li>
        <li><a href="#choosing-an-image-format">이미지 포맷은 어떻게 선택해야 하나요?</a></li>
        <li><a href="#the-humble-jpeg">겸손한 JPEG</a></li>
        <li><a href="#jpeg-compression-modes">JPEG 압축 모드</a>
                <ul>
                        <li><a href="#the-advantages-of-progressive-jpegs">점진적 JPEG 모드의 장점</a></li>
                        <li><a href="#whos-using-progressive-jpegs-in-production">점진적 JPEG 모드는 실제로 어디서 사용 중인가요?</a></li>
                        <li><a href="#the-disadvantages-of-progressive-jpegs">점진적 JPEG 모드의 단점</a></li>
                        <li><a href="#how-to-create-progressive-jpegs">점진적 JPEG 모드로 압축은 어떻게 하나요?</a></li>
                        <li><a href="#chroma-subsampling">채도(색상) 서브샘플링</a></li>
                        <li><a href="#how-far-have-we-come-from-the-jpeg">기존의 JPEG에서 얼마나 발전한 상태인가요?</a></li>
                        <li><a href="#optimizing-jpeg-encoders">JPEG 인코더 최적화</a></li>
                        <li><a href="#what-is-mozjpeg">MozJPEG은 뭐죠?</a></li>
                        <li><a href="#what-is-guetzli">Guetzli는 뭐죠?</a></li>
                        <li><a href="#mozjpeg-vs-guetzli">MozJPEG과 Guetzli를 비교해 본다면?</a></li>
                        <li><a href="#butteraugli">Butteraugli</a></li>
                </ul>
        </li>
        <li><a href="#what-is-webp">WebP가 뭔가요?</a>
                <ul>
                        <li><a href="#how-does-webp-perform">WebP의 성능은 어떤가요?</a></li>
                        <li><a href="#whos-using-webp-in-production">WebP는 실제로 어디서 사용 중인가요?</a></li>
                        <li><a href="#how-does-webp-encoding-work">WebP 인코딩 원리는 뭔가요?</a></li>
                        <li><a href="#webp-browser-support">WebP 브라우저 지원상황</a></li>
                        <li><a href="#how-do-i-convert-to-webp">이미지를 어떻게 WebP로 변환시키죠?</a></li>
                        <li><a href="#how-do-i-view-webp-on-my-os">제 OS에서 WebP 이미지를 어떻게 열어보나요?</a></li>
                        <li><a href="#how-do-i-serve-webp">WebP는 어떻게 서버에서 전송해야 하나요?</a></li>
                </ul>
        </li>
        <li><a href="#svg-optimization">SVG 최적화</a></li>
        <li><a href="#avoid-recompressing-images-lossy-codecs">손실 코덱으로 이미지를 재압축하지 마세요</a></li>
        <li><a href="#reduce-unnecessary-image-decode-costs">불필요한 디코딩 및 크기변환 하지 않기</a>
                <ul>
                        <li><a href="#delivering-hidpi-with-srcset"><code>srcset</code>으로 HiDPI 이미지 전달하기</a></li>
                        <li><a href="#art-direction">아트 디렉션</a></li>
                </ul>
        </li>
        <li><a href="#color-management">색상 관리</a></li>
        <li><a href="#image-sprites">이미지 스프라이트 기법</a></li>
        <li><a href="#lazy-load-non-critical-images">중요하지 않은 이미지는 레이지 로딩</a></li>
        <li><a href="#display-none-trap"><code>display: none;</code> 함정 피하기</a></li>
        <li><a href="#image-processing-cdns">이미지 처리 CDN을 사용해도 될까요?</a></li>
        <li><a href="#caching-image-assets">이미지 에셋 캐싱하기</a></li>
        <li><a href="#preload-critical-image-assets">중요한 이미지는 프리로딩(preloading)</a></li>
        <li><a href="#performance-budgets">이미지 성능 예산 짜기</a></li>
        <li><a href="#closing-recommendations">글을 마치며 추천 사항 몇 가지</a></li>
        <li><a href="#trivia">트리비아</a></li>
</ul>
</p>
</details>

### <a id="introduction" href="#introduction">서론</a>

**웹 리소스 용량이 불어나는 데는 여전히 이미지가 일등 공신입니다.**

이미지는 대용량으로 올라가는 경우가 많아서 인터넷 대역폭에서 막대한 지분율을 차지합니다. [HTTP Archive](http://httparchive.org/)에 따르면, 웹 페이지를 가져올 때 전송받은 데이터 중 60%는 JPEG, PNG, GIF 이미지가 차지합니다. 2017년 7월 기준, 한 사이트에서 평균 3.0MB의 콘텐츠를 불러올 때, 이미지가 이 중에서 [1.7MB](http://httparchive.org/interesting.php#bytesperpage)를 차지합니다.

Tammy Everts의 글에 따르면, 페이지에 이미지를 추가하거나 기존 이미지를 더 크게 만드는 경우 전환율(CVR, Conversion Rate)이 [높아진다고 합니다](https://calendar.perfplanet.com/2014/images-are-king-an-image-optimization-checklist-for-everyone-in-your-organization/). 하지만 이미지가 웹에서 자취를 감출 것 같지는 않으므로, 용량 증가량을 최소화하기 위해 효과적인 압축 전략 마련에 시간과 비용을 투자하는 일이 중요해 졌습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image00.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image00.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image00.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image00.jpg"
        alt="페이지 당 이미지 수가 적을수록 전환율이 높아집니다. 장당 평균 19장의 이미지가 있는 경우, 평균 31장의 이미지가 있을 때보다 전환율이 높았습니다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image00.jpg"/>
</noscript>
</picture>
<figcaption>2016년 [Soasta/Google 연구](https://www.thinkwithgoogle.com/marketing-resources/experience-design/mobile-page-speed-load-time/)에 따르면, 전환율에서 이미지가 2번째로 중요한 역할을 합니다. 또한, 이미지 수가 기존 대비 38% 정도 줄은 페이지에서 가장 전환율이 높았습니다.</figcaption>
</figure>

이미지 최적화 작업은 파일 크기를 줄이는 기법을 여러 개 같이 사용하여 행합니다. 결국, 여러분 서비스에 필요한 이미지의 해상도에 따라 최적화 작업 방법이 달라집니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/image-optimisation"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/image-optimisation"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/image-optimisation" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/image-optimisation"
        alt="이미지 최적화 과정은 다양한 기법들로 구성되어 있습니다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/image-optimisation"/>
</noscript>
</picture>
<figcaption><strong>이미지 최적화:</strong> 적절한 포맷을 선택하고, 신중하게 압축한 후, 중요한 이미지를 레이지 로딩 대상 이미지보다 먼저 로딩합니다.</figcaption>
</figure>

일반적인 이미지 최적화 과정은 압축 단계, [`<picture>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture)/[`<img srcset>`](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)을 통한 화면 크기에 따른 반응형 이미지 제공 단계, 그리고 이미지 디코딩 비용 절약을 위한 리사이징 단계로 이루어져 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502834117/chart_naedwl.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502834117/chart_naedwl.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502834117/chart_naedwl.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502834117/chart_naedwl.jpg"
        alt="HTTP Archive 데이터를 바탕으로 그린 잠재적인 이미지 데이터 절약량 히스토그램 입니다. 백분위 95%에서 잠재 용량 절약량이 30KB 입니다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502834117/chart_naedwl.jpg"/>
</noscript>
</picture>
<figcaption>[HTTP Archive](http://jsfiddle.net/rviscomi/rzneberp/embedded/result/)에 따르면, 백분위 95%(오른쪽 누적 분포함수 축 참조)에서 이미지 한 장 당 30KB 정도를 잠재적인 데이터 절약량으로 볼 수 있습니다!</strong></figcaption>
</figure>

이미지 여러 장을 한꺼번에 최적화하는 작업 역시 얼마든지 더 편하게 하는 방법이 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502519576/essential-image-optimization/image-optim.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502519576/essential-image-optimization/image-optim.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502519576/essential-image-optimization/image-optim.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502519576/essential-image-optimization/image-optim.jpg"
        alt="맥 OS에서 ImageOptim으로 이미지 여러 장을 압축해 본 결과, 용량의 50% 이상을 줄일 수 있었습니다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502519576/essential-image-optimization/image-optim.jpg"/>
</noscript>
</picture>

<figcaption>ImageOptim는 무료 툴입니다. 최신 압축 기술을 이용하며, 불필요한 EXIF 메타 데이터를 제거하여 이미지 크기를 감소시킵니다.</figcaption>
</figure>

디자이너분들을 위한 [ImageOptim plugin for Sketch](https://github.com/ImageOptim/Sketch-plugin)도 있습니다. Sketch에서 에셋을 내보낼 때 최적화시킬 수 있는 플러그인입니다. 이걸 사용하면 시간을 아주 많이 절약할 수 있을 것 같네요.

### <a id="do-my-images-need-optimization" href="#do-my-images-need-optimization">이미지 최적화가 필요한지 어떻게 알 수 있을까요?</a>

[WebPageTest.org](https://www.webpagetest.org/)에 들어가서 사이트 검사(audit) 기능을 실행해 본다면, 어느 부분에서 이미지 최적화에 대한 여지가 남아있는지 살펴볼 수 있습니다. (아래 그림에서 ‘Compress Images’를 보세요)

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image1.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image1.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image1.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image1.jpg"
        alt="WebPage 사이트에서는 테스트 결과 페이지의 compress images 섹션에 이미지 압축 검사 결과를 표시해 줍니다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image1.jpg"/>
</noscript>
</picture>

<figcaption>WebPageTest 보고서의 ‘Compress Images’ 섹션에는 더 효율적으로 압축을 해 볼 수 있는 이미지 목록이 첨부되어 있고, 압축 후에 얼마만큼 파일 사이즈를 줄일 수 있는지에 대한 추정치가 제시되어 있습니다.</figcaption>
</figure>

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image2.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image2.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image2.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image2.jpg"
        alt="webpagetest 보고서에 제시된 이미지 압축관련 항목" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image2.jpg"/>
</noscript>
</picture>
</figure>

구글의 [Lighthouse](https://developers.google.com/web/tools/lighthouse/)를 사용하면 성능 모범 사례를 얼마나 따랐는지 검사할 수 있습니다. 이 툴에는 이미지 최적화 품질에 대한 검사 기능이 포함되어 있으며, 압축의 여지가 남아 있거나 화면에 나오지 않아 레이지로딩 할 수 있는 이미지들을 보여줍니다.

Chrome 60 버전부터는 개발자 도구의 [Audits 패널](https://developers.google.com/web/updates/2017/05/devtools-release-notes#lighthouse) 안에 Lighthouse가 들어가게 되었습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/hbo.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/hbo.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/hbo.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/hbo.jpg"
        alt="Lighthouse를 사용해 HBO.com 사이트 검사해 본 결과. 이미지 최적화 관련 추천 사항이 나와 있다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/hbo.jpg"/>
</noscript>
</picture>
<figcaption>Lighthouse로 웹 성능, 모범 사례 및 점진적 웹 앱 기능에 대한 품질을 검사할 수 있습니다.</figcaption>
</figure>

Lighthouse 외에도 이미지 심층 조사 기능이 포함된 [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/)나 Cloudinary의 [Website Speed Test](https://webspeedtest.cloudinary.com/) 같은 성능 품질 검사 툴 역시 이미 알고 계실 것 같네요.

## <a id="choosing-an-image-format" href="#choosing-an-image-format">이미지 포맷은 어떻게 선택해야 하나요?</a>

이미 Ilya Grigorik이 쓴 훌륭한 [image optimization guide](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization)에도 언급되어 있듯이, 이미지의 ‘올바른 포맷’이란 눈으로 볼 수 있는 결과물과 기능적인 요구 사항이 더해져서 만들어지는 것입니다. 작업하실 때 쓰는 이미지가 벡터인가요, 아니면 래스터인가요?

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502469573/essential-image-optimization/rastervvector.png"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502469573/essential-image-optimization/rastervvector.png"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502469573/essential-image-optimization/rastervvector.png" />

<img
        class="lazyload very-small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502469573/essential-image-optimization/rastervvector.png"
        alt="벡터 대 래스터 이미지"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502469573/essential-image-optimization/rastervvector.png"/>
</noscript>
</picture>
</figure>

[래스터 그래픽](https://en.wikipedia.org/wiki/Raster_graphics)은 픽셀의 사각형 격자구조에 각 픽셀값을 인코딩하여 이미지를 표현합니다. 래스터 형식으로 만든 이미지는 해상도 조절 및 크기 확대/축소에 영향을 받습니다. 극사실주의(Photorealism, 사물을 사진처럼 정확하고 상세하게 묘사하는 예술 기법)적 이미지를 사용해야 할 때, WebP나 지원 상황이 괜찮은 JPEG/PNG 포맷을 사용하면 이미지의 의도가 잘 표현됩니다. 이 세 포맷은 래스터 그래픽을 다루는 데 최적화되어 있습니다. Guetzli과 MozJPEG, 그리고 앞에 나온 최적화 기법들 역시 래스터 그래픽에 사용하기 적합합니다.

[벡터 그래픽](https://en.wikipedia.org/wiki/Vector_graphics)은 형태와 이미지를 표현할 때 점, 선, 다각형을 사용합니다. 이를 통해 간단한 기하학적 모양(예: 로고)을 표현합니다. SVG 같은 벡터 그래픽을 사용하면 고해상도로 이미지를 제공할 수 있고 얼마든지 확대/축소가 가능합니다.

잘못 선택한 포맷 때문에 나중에 대가를 치러야 할 수도 있습니다. 이미지 포맷을 적절하게 고르는 일은 쉬운 일이 아니므로, 선택한 포맷 말고 다른 이미지 포맷을 사용하면 비용을 얼마나 더 절약할 수 있을지 신경 써서 테스트해야 합니다.

이미지 포맷 비교 시 고려해야 할 트레이드 오프에 대해 알아보려면 Jeremy Wagner의 [trade-offs](http://jlwagner.net/talks/these-images/#/2/2) 발표 내용을 참조하시면 됩니다.

## <a id="the-humble-jpeg" href="#the-humble-jpeg">겸손한 JPEG</a>

[JPEG](https://en.wikipedia.org/wiki/JPEG)는 아마 세계에서 가장 많이 사용되는 이미지 형식일 가능성이 큽니다. 앞서 언급했듯이, HTTP Archive에서 크롤링 한 사이트들의 [이미지 중 45%](http://httparchive.org/interesting.php)는 JPEG 포맷입니다. 여러분이 사용하고 계시는 휴대 전화, 디지털 SLR, 옛날 웹캠 등등 거의 모든 기기가 JPEG 포맷을 지원합니다. JPEG은 아주 오래된 포맷이기도 한데, 처음 출시된 날은 1992년으로 거슬러 올라갑니다. 그 시절에는 JPEG의 사양을 향상하기 위한 연구가 많이 진행되었습니다.

JPEG은 용량을 줄이기 위해 이미지 정보 일부를 날리는 손실 압축 알고리즘입니다. 가능한 JPEG 파일 크기는 작게 유지하면서 시각적인 선명도(visual fidelity)는 최대로 보존하려는 시도가 많이 있었습니다.

**상황에 따른 알맞은 이미지 품질은 어느 정도일까요?**

JPEG 같은 포맷은 다양한 색 영역을 표현해야 하는 사진이나 이미지에 가장 적합합니다. 최적화 툴은 대부분 여러분이 만족할 수준의 압축 레벨로 세팅할 수 있습니다. 압축률을 높이면 파일 크기는 줄어들지만 부자연스러운 흔적(artifact, 아티팩트), 후광(halos), 혹은 블록 열화현상(blocky degrading)이 이미지에 생길 수도 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image5.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image5.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image5.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image5.jpg"
        alt="JPEG 압축으로 인한 아티팩트는 이미지 품질을 최고에서 최하까지 내리면 내릴수록 눈에 띄게 증가합니다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image5.jpg"/>
</noscript>
</picture>
<figcaption>JPEG: JPEG 압축 때문에 발생하는 아티팩트는 이미지 품질을 최고에서 최하로 내리면 내릴수록 더 많이 발생합니다. 이미지 품질 점수 결과는 사용하는 툴에 따라 매우 달라질 수 있음을 주의하시기 바랍니다.</figcaption>
</figure>

품질을 설정할 때는 다음 목록을 참조해 여러분이 준비한 이미지에 맞는 품질로 맞춰보도록 하세요.

*   **최상** – 품질이 대역폭보다 중요 할 때. 디자인에서 이미지가 중요한 위치를 차지하거나, 최대 해상도로 보여주어야 하는 경우가 이에 해당합니다.
*   **상** – 이미지 크기를 조금 줄여서 내보내고 싶지만 그렇다고 품질이 너무 떨어지는 것은 원하지 않을 때. 사용자들에게 보여주는 이미지 품질이 아직 중요한 경우입니다.
*   **하** – 대역폭에 신경을 써야 하므로 이미지 품질이 떨어져도 괜찮다고 생각되는 경우. 네트워크 상태가 좋지 않거나 종종 끊길 때 적합한 품질입니다.
*   **최하** – 다른 무엇보다 대역폭 사용량을 절약하는 것이 가장 중요한 경우. 이미지 품질보다 네트워크 속도가 사용자들에게 더 중요한 경우입니다. 이 품질을 택하면 페이지 로딩 속도가 빨라지므로 이미지 품질 저하는 참을만합니다.

자, 이제 다음으로 JPEG 압축 모드에 대해 알아보겠습니다. 압축 모드는 체감 성능에 큰 영향을 줄 수 있으므로 이에 대해 얘기해 보도록 하겠습니다.

<aside class="note"><b>알아두기:</b> 때로는 사용자에게 보여줄 이미지 품질을 실제로 필요한 정도보다 높게 가정할 때도 있습니다. 이미지 품질이란 이상적인 원본 이미지에서 얼마나 벗어날 것인가를 결정하는 척도라고 생각할 수도 있겠네요. 이 역시 사람마다 생각하는 정도가 다를겁니다.</aside>

## <a id="jpeg-compression-modes" href="#jpeg-compression-modes">JPEG 압축 모드</a>

JPEG 이미지 포맷에는 여러 가지 [압축 모드](http://cs.haifa.ac.il/~nimrod/Compression/JPEG/J5mods2007.pdf)가 있습니다. 인기 있는 모드 세 가지를 꼽아보면, 순차적(baseline, 혹은 sequential) JPEGs, 점진적 JPEG(Progressive JPEG, PJPEG), 그리고 무손실(lossless) 모드 입니다.

**순차적 JPEG 모드와 점진적 JPEG 모드의 차이점은 뭔가요?**

순차적 JPEG(대부분의 이미지 편집 및 최적화 툴에서 설정된 기본 모드)은 인코딩과 디코딩 방향이 위에서 아래로 진행되는 모드로 비교적 단순합니다. 이미지가 순차적 JPEG 모드로 압축되어 있는데 네트워크 속도가 느리거나 종종 끊긴다면, 사용자는 이미지의 가장 상단 부분을 맨 처음 보게 될 것이고 점점 로딩이 진행될수록 아랫부분이 나타나게 됩니다. 무손실 JPEG 모드는 순차적 JPEG과 비슷하나 압축률이 더 낮습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image6.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image6.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image6.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image6.jpg"
        alt="이미지가 위에서부터 아래로 로딩되는 순차적 JPEG 모드" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image6.jpg"/>
</noscript>
</picture>
<figcaption>순차적 JPEG 모드는 이미지가 위에서부터 아래로 로딩이 되지만, 점진적 JPEG 모드에서는 처음에 흐릿한 이미지로 나타났다가 점차 또렷한 이미지로 대체됩니다.</figcaption>
</figure>

점진적 JPEG 모드에서는 이미지를 여러 번 나누어 스캔합니다. 첫 번째 스캔에서는 흐릿하게 나타나는 낮은 품질의 이미지가 나타나고, 스캔이 계속 진행될수록 이미지 품질이 점차 좋아지게 됩니다. 이미지를 ‘점진적으로’ 정제하는 과정이라 생각하시면 됩니다. 각 ‘스캔’ 단계마다 이미지의 세부묘사가 정밀해집니다. 스캔한 결과물을 합쳐 놓으면 처음 설정한 품질대로 이미지가 나타나게 됩니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image7.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image7.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image7.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image7.jpg"
        alt="점진적 JPEG은 이미지를 저해상도에서 고해상도까지 점차 불러들입니다" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image7.jpg"/>
</noscript>
</picture>
<figcaption>순차적 JPEG 모드는 이미지를 위에서 아래로 로딩합니다. 점진적 JPEG 모드는 저해상도(흐린 상태)에서 고해상도로 로딩합니다. Pat Meenan의 글 [interactive tool](http://www.patrickmeenan.com/progressive/view.php?img=https%3A%2F%2Fwww.nps.gov%2Fplanyourvisit%2Fimages%2FGrandCanyonSunset_960w.jpg)을 보면 이에 대한 테스트와 점진적 JPEG 모드의 스캔 방식에 대해 알아볼 수 있습니다.</figcaption>
</figure>

무손실 JPEG 최적화는 디지털 카메라나 편집 도구에서 추가한 [EXIF 데이터를 제거](http://www.verexif.com/en/)하거나, 이미지 [허프만 테이블](https://en.wikipedia.org/wiki/Huffman_coding)을 최적화하거나, 이미지를 스캔을 다시 하는 식으로 진행할 수 있습니다. [jpegtran](http://jpegclub.org/jpegtran/) 같은 툴을 사용하면 이미지 품질 저하 없이 압축 데이터를 재배열하여 무손실 압축을 수행할 수 있습니다. [jpegrescan](https://github.com/kud/jpegrescan), [jpegoptim](https://github.com/tjko/jpegoptim), [mozjpeg](https://github.com/mozilla/mozjpeg)(조금 있다가 다룰 예정입니다)과 같은 툴 역시 무손실 JPEG 압축을 지원합니다.

### <a id="the-advantages-of-progressive-jpegs" href="#the-advantages-of-progressive-jpegs">점진적 JPEG 모드의 장점</a>

점진적 JPEG 모드에서는 이미지를 저해상도로 ‘미리 보기’ 할 수 있으므로 체감 성능을 향상시킬 수 있습니다. 사용자로서는 적응형 이미지(adaptive image)보다 로딩 속도가 빠르다 여길 수도 있습니다.

속도가 좀 더 느린 3G를 사용하고 있다면, 파일 일부만 받았더라도 사용자는 이미지가 무엇인지 (대략적으로나마) 볼 수 있으며, 이를 보면서 이미지 전체를 다 받을 때까지 기다릴지 말지 결정 내릴 수 있습니다. 이런 경우, 순차적 JPEG 모드로 압축해 이미지를 위에서부터 아래로 보여줄 때보다 더 좋은 사용자 경험을 제공해 줄 수도 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504993129/essential-image-optimization/pjpeg-graph.png"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1504993129/essential-image-optimization/pjpeg-graph.png"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504993129/essential-image-optimization/pjpeg-graph.png" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504993129/essential-image-optimization/pjpeg-graph.png"
        alt="점진적 JPEG 모드로 바꿀 때 사용자 대기 시간에 미친 영향" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504993129/essential-image-optimization/pjpeg-graph.png"/>
</noscript>
</picture>
<figcaption>[페이스북은 2015년에 점진적 JPEG 모드로 전환(iOS 앱)](https://code.facebook.com/posts/857662304298232/faster-photos-in-facebook-for-ios/)하여 데이터 사용량 중 10%를 줄이는 효과를 봤습니다. 위 도표에서 볼 수 있듯이, 품질 좋은 이미지를 전보다 15% 더 빨리 사용자에게 보여주고 체감 로딩 시간을 최적화하였습니다.</figcaption>
</figure>

점진적 JPEG 모드를 사용하면 더 효율적인 압축을 할 수 있어서, 이미지 용량이 10KB 이상이 될 경우, 순차적 JPEG 모드로 압축할 때보다 대역폭 사용량이 [2-10%](http://www.bookofspeed.com/chapter5.html) 정도 줄어들게 됩니다. 점진적 JPEG 압축 모드가 높은 압축률을 유지할 수 있는 비결은 각 스캔 단계마다 선택적으로 지닌 [허프만 테이블](https://en.wikipedia.org/wiki/Huffman_coding)에 있습니다. 최신 JPEG 인코더(예: [libjpeg-turbo](http://libjpeg-turbo.virtualgl.org/), MozJPEG 등)는 점진적 JPEG 압축 모드의 이런 유연한 장점을 빌려 데이터를 좀 더 효율적으로 정리합니다.

<aside class="note"><b>알아두기:</b> 왜 점진적 JPEG 모드가 압축률이 더 높을까요? 순차적 JPEG 모드는 한 번에 한 블록만 인코딩합니다. 점진적 JPEG 모드를 사용하면 두 개 이상의 블록에서 유사한 [이산 코사인 변환(Discrete Cosine Transform)](https://en.wikipedia.org/wiki/Discrete_cosine_transform) 계수를 함께 인코딩할 수 있으므로 압축률이 향상됩니다.</aside>

### <a id="whos-using-progressive-jpegs-in-production" href="#whos-using-progressive-jpegs-in-production">점진적 JPEG 모드는 실제로 어디서 사용 중인가요?</a>

*   [Twitter.com에서는 점진적 JPEG 모드를](https://www.webpagetest.org/performance_optimization.php?test=170717_NQ_1K9P&run=2#compress_images) 기본 품질 85%로 하여 사용 중입니다. 사용자 체감 지연 시간(스캔을 처음 한 시점부터 전체 로딩 시간 측정)에 대해 조사를 진행한 결과, 트위터 내에서 수요가 있는 저용량의 작은 이미지 파일을 다룰 때 점진적 JPEG 모드를 사용하면 변환 및 디코딩 시간이 허용치 내로 걸린다는 사실을 발견했습니다.
*   [Facebook에서는 iOS 앱 내에서 점진적 JPEG 모드를 사용하여 이미지를 전달합니다](https://code.facebook.com/posts/857662304298232/faster-photos-in-facebook-for-ios/). 데이터 사용량이 10%가량 감소하였고 품질 좋은 이미지는 속도가 15% 정도 빠르게 화면에 나온다고 합니다.
*   [Yelp는 점진적 JPEG로 압축 모드를 바꿨습니다.](https://engineeringblog.yelp.com/2017/06/making-photos-smaller.html) 그리고 그 결과 이미지 크기 절감 성공량 중 4.5% 정도는 압축 모드 전환 덕분이라고 합니다. 또한 MozJPEG을 사용함으로써 추가로 13.8% 정도의 데이터 사용량을 아낄 수 있었다고 합니다.

[Pinterest](https://pinterest.com)와 같이 이미지가 중요한 많은 사이트 역시 실제로 점진적 JPEG 모드를 사용하고 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1506382472/essential-image-optimization/pinterest-loading.png"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1506382472/essential-image-optimization/pinterest-loading.png"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1506382472/essential-image-optimization/pinterest-loading.png" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1506382472/essential-image-optimization/pinterest-loading.png"
        alt="Pinterest에서는 JPEG 이미지를 모두 점진적 모드로 인코딩합니다. 이렇게 하면 각 스캔 단계별로 이미지가 로딩되기 때문에 사용자 경험 최적화 할 수 있습니다." />
<noscript>
  <img src="http://res.cloudinary.com/ddxwdqwkr/image/upload/v1506382472/essential-image-optimization/pinterest-loading.png
"/>
</noscript>
</picture>
<figcaption>Pinterest에서는 JPEG 이미지를 모두 점진적 모드로 인코딩합니다. 이렇게 하면 각 스캔 단계별로 이미지가 로딩되기 때문에 사용자 경험을 향상할 수 있습니다.</figcaption>
</figure>

### <a id="the-disadvantages-of-progressive-jpegs" href="#the-disadvantages-of-progressive-jpegs">점진적 JPEG 모드의 단점</a>

점진적 JPEG 모드는 순차적 JPEG 모드 보다 디코딩 속도가 느릴 수 있으며, 때로는 순차적 JPEG 모드에서 걸리는 시간보다 3배의 시간이 소요되기도 합니다. 이런 단점은 강력한 CPU를 갖춘 데스크톱 컴퓨터에서는 문제가 되지 않지만, 리소스가 제한적인 저전력 모바일 장치에서는 문제가 될 수 있습니다. 점진적 JPEG 모드를 사용하면 기본적으로 이미지를 여러 번 디코딩하는 셈이 되므로, 불완전한 레이어를 여러 번 화면에 표시해야 하는 일이 생기게 됩니다. 따라서 중복된 작업이 CPU 사이클 일부를 차지하게 될 수도 있습니다.

점진적 JPEG 모드가 *항상* 크기가 작은 이미지를 만들어내는 것도 아닙니다. 썸네일과 같이 매우 작은 이미지의 경우, 점진적 JPEG 모드로 압축된 이미지는 순차적 JPEG 모드로 압축된 이미지보다 용량이 큽니다. 그러므로 작은 썸네일 이미지의 경우, 점진적 모드는 사실 그다지 장점이 없을 수도 있습니다.

이 말은 즉, 점진적 JPEG 제공 여부를 결정할 때는 먼저 파일 크기와 네트워크 지연 시간, 그리고 CPU 사이클 사이의 올바른 균형을 조사하여 찾아낼 필요가 있다는 것입니다.

<aside class="note"><b>알아두기:</b> 점진적 JPEG을 비롯한 모든 JPEG는 때때로 모바일 장치에서 하드웨어 디코딩이 가능합니다. RAM에 영향을 미치지는 않지만, CPU 관련 부작용을 발생시킬 수도 있습니다. 모든 안드로이드 기기가 하드웨어 가속을 지원하는 것은 아니지만, 고급 기기와 iOS 기기는 전부 하드웨어 가속을 지원합니다.</aside>

이미지 로딩이 완전히 끝난 시점이 언제인지 알아내기 어려울 수도 있어 일부 사용자들은 이 점을 점진적 JPEG의 단점으로 봅니다. 이를 단점으로 볼지 아닐지는 사용자마다 크게 다를 수 있으므로, 여러분의 사용자들에게 적합한 방식은 무엇인지 연구해보세요.

### <a id="how-to-create-progressive-jpegs" href="#how-to-create-progressive-jpegs">점진적 JPEG 모드로 압축은 어떻게 하나요?</a>

[ImageMagick](https://www.imagemagick.org/), [libjpeg](http://libjpeg.sourceforge.net/), [jpegtran](http://jpegclub.org/jpegtran/), [jpeg-recompress](https://github.com/danielgtaylor/jpeg-archive), [imagemin](https://github.com/imagemin/imagemin) 같은 툴과 라이브러리는 점진적 JPEG 모드로 내보내기 기능을 제공합니다. 이미 이미지 최적화 파이프라인을 구축해 놓은 경우, 점진적 JPEG 모드 압축 기능 추가는 꽤 간단하게 할 수 있습니다.

```js
const gulp = require('gulp');
const imagemin = require('gulp-imagemin');

gulp.task('images', function () {
    return gulp.src('images/*.jpg')
        .pipe(imagemin({
            progressive: true
        }))
        .pipe(gulp.dest('dist'));       
});
```

대부분의 이미지 편집 툴에서는 이미지를 기본적으로 순차적 JPEG 모드로 저장합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_crop,h_477,w_2128,y_0/c_scale,w_500,/v1502426282/essential-image-optimization/photoshop.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_crop,h_477,w_2128,y_0/c_scale,w_900/v1502426282/essential-image-optimization/photoshop.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_crop,h_477,w_2128,y_0/v1502426282/essential-image-optimization/photoshop.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_crop,h_477,w_2128,y_0/v1502426282/essential-image-optimization/photoshop.jpg"
        alt="포토샵에서는 파일 내보내기 메뉴에서 점진적 JPEG 모드로 내보내기 기능을 선택할 수 있습니다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_crop,h_477,w_2128,y_0/v1502426282/essential-image-optimization/photoshop.jpg"/>
</noscript>
</picture>
<figcaption>대부분의 이미지 편집 툴에서는 이미지를 기본적으로 순차적 JPEG 모드로 저장합니다. 포토샵에서 만든 이미지를 점진적 JPEG 이미지로 저장하려면, File -> Export -> Save for Web (legacy) 메뉴에서 Progressive 옵션을 선택해 주세요. 스케치 앱에서도 점진적 JPEG으로 이미지 추출이 가능합니다. export as JPG 메뉴에서 ‘Progressive’ 체크 박스를 선택해 주세요.</figcaption>
</figure>

### <a id="chroma-subsampling" href="#chroma-subsampling">채도 서브샘플링(Chroma Subsampling)</a>

우리의 눈은 밝기(luminance, 줄여서 luma)보다는 채도 쪽의 색상 세부 정보가 손실되더라도 이를 실제보다 약하게 인식하는 경향이 있습니다. [채도 서브샘플링](https://en.wikipedia.org/wiki/Chroma_subsampling)은 일종의 압축 방식인데, 신호의 밝기는 손상시키지 않으면서 색상 정확도를 낮추어 파일 크기를 줄입니다. 이미지 품질 손상 없이 [15~17%](https://calendar.perfplanet.com/2015/why-arent-your-images-using-chroma-subsampling/)까지 크기를 줄인 사례도 종종 있었다고 합니다. JPEG 이미지 압축 시에 옵션으로 선택할 수 있는 방식이기도 합니다. 또한, 서브샘플링으로 이미지 메모리 사용량을 줄일 수 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1503683718/essential-image-optimization/luma-signal.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1503683718/essential-image-optimization/luma-signal.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503683718/essential-image-optimization/luma-signal.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503683718/essential-image-optimization/luma-signal.jpg"
        alt="신호 = 색상 + 밝기" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503683718/essential-image-optimization/luma-signal.jpg"/>
</noscript>
</picture>
</figure>

명암(contrast)은 이미지 안의 형태를 완성 시키는 역할을 하므로, 이를 조절하는 밝기는 꽤 중요도가 높습니다. 낡거나 필터링을 거친 흑백 사진에는 색상 정보가 빠져 있을지도 모르나, 밝기가 존재하므로 색상 정보가 있을 때만큼 사진의 세부묘사를 완성할 수 있습니다. 시각적 인식에 있어서 채도(색상)는 밝기보다 덜 중요합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1503683936/essential-image-optimization/no-subsampling.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1503683936/essential-image-optimization/no-subsampling.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503683936/essential-image-optimization/no-subsampling.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503683936/essential-image-optimization/no-subsampling.jpg"
        alt="JPEG 포맷은 다양한 서브샘플링 타입을 지원합니다. 서브샘플링이 없을 수도 있고, 수평으로만 하거나, 수평 및 수직으로 할 수도 있습니다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503683936/essential-image-optimization/no-subsampling.jpg"/>
</noscript>
</picture>
<figcaption>JPEG 포맷은 다양한 서브샘플링 타입을 지원합니다. 서브샘플링이 없을 수도 있고, 수평으로만 하거나, 수평과 수직으로 둘 다 할 수도 있습니다. 위 그림은 Frédéric Kayser의 [JPEGs for the horseshoe crabs](http://frdx.free.fr/JPEG_for_the_horseshoe_crabs.pdf)에서 발췌한 것입니다.</figcaption>
</figure>

서브샘플링과 관련하여 자주 언급되는 샘플이 몇 개 있습니다. 보통은 `4:4:4`, `4:2:2`, `4:2:0`입니다. 그런데 이 숫자가 나타내는 것은 뭘까요? 서브샘플이 A:B:C의 형식을 취한다고 해봅시다. A는 한 행에 들어가는 픽셀 수를 의미하며, JPEG에서는 보통 4입니다. B는 첫째 행에 나오는 색상 수를 의미하고, C는 두 번째 행에 나타나는 색상 수 입니다.

* `4:4:4`는 압축하지 않아 색상과 밝기가 완벽히 재현됩니다.
* `4:2:2`는 가로로 반절만 샘플링을 하고 세로는 그대로 보존합니다.
* `4:2:0`는 첫째 행의 픽셀 중 반절을 기준으로 색상 샘플을 취하고, 두 번째 행은 날려버립니다.

<aside class="note"><b>알아두기:</b> jpegtran과 cjpeg은 밝기 및 채도 품질을 별도로 설정할 수 있습니다. `-sample` 플래그를 붙이시면 됩니다. (예: `-sample 2x1`). 유용한 옵션값은 다음과 같습니다. `-sample 2x2` 서브샘플링은 사진에 아주 적합합니다. `-sample 1x1`은 서브샘플링을 하지 않는 옵션인데, 스크린샷과 배너, 버튼에 적합합니다. 무슨 옵션을 주어야 할지 모르겠다면 최후의 보루로 `2x1`을 사용하시면 됩니다.</aside>

채도 쪽의 픽셀 수를 줄임으로써, 컬러 쪽 용량이 상당히 줄어들게 되므로 이는 곧 파일 크기의 감소로 이어지게 됩니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1503684781/essential-image-optimization/subsampling.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1503684781/essential-image-optimization/subsampling.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503684781/essential-image-optimization/subsampling.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503684781/essential-image-optimization/subsampling.jpg"
        alt="품질 80의 JPEG에서 채도 서브샘플링 설정." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503684781/essential-image-optimization/subsampling.jpg"/>
</noscript>
</picture>
<figcaption>품질 80의 JPEG에서 채도 서브샘플링 설정.</figcaption>
</figure>

대부분의 이미지 포맷에서 채도 서브샘플링 사용을 고려해볼 만합니다. 몇 가지 잘 알려진 예외가 있긴 합니다. 서브샘플링은 우리 눈의 한계를 바탕으로 고안된 방법이므로, 밝기만큼 색상의 세부묘사가 중요한 이미지를(예: 의학 사진) 압축할 때는 별로 좋은 방법이 아닙니다.

글씨가 들어간 이미지 역시 텍스트 서브샘플링이 잘못되면 가독성이 떨어지는 부작용을 겪을 수도 있습니다. JPEG은 사진을 부드럽게 뭉개서 효과를 보고자 만들어진 포맷이기 때문에, 텍스트의 가장자리가 선명할수록 손상 없이 압축하기가 어려워집니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1503684410/essential-image-optimization/Screen_Shot_2017-08-25_at_11.06.27_AM.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1503684410/essential-image-optimization/Screen_Shot_2017-08-25_at_11.06.27_AM.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503684410/essential-image-optimization/Screen_Shot_2017-08-25_at_11.06.27_AM.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503684410/essential-image-optimization/Screen_Shot_2017-08-25_at_11.06.27_AM.jpg"
        alt="Be careful when using heavy subsampling with images containing text" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503684410/essential-image-optimization/Screen_Shot_2017-08-25_at_11.06.27_AM.jpg"/>
</noscript>
</picture>
<figcaption>[Understanding JPEG](http://compress-or-die.com/Understanding-JPG/) 글에 따르면, 텍스트가 포함된 이미지에 서브샘플링 적용 시 4:4:4 (1x1) 옵션으로만 선택하기를 권장합니다.</figcaption>
</figure>

트리비아: 채도 서브샘플링의 정확한 방법은 JPEG 명세서에 정의가 되지 않았기 때문에 디코더마다 구현 방식이 다릅니다. MozJPEG과 libjpeg-turbo는 둘 다 축적(scaling) 방식을 채택하였습니다. 예전 버전 libjpeg에서는 다른 방식을 사용했었는데, 색상에 고리 모양 아티팩트(ringing artifact)를 추가하는 방식이었습니다.

<aside class="note"><b>알아두기:</b> 포토샵에서는 '웹용으로 저장(Save for web)' 기능 사용 시 채도 서브샘플링을 자동으로 설정합니다. 이미지 품질이 51-100 사이로 설정되었다면 서브샘플링은 전혀 적용되지 않은 상태입니다(`4:4:4`). 이보다 품질이 낮다면 `4:2:0` 서브샘플링이 사용됩니다. 품질을 51에서 50으로 낮출 때 파일 크기가 상당히 줄어드는 것을 볼 수 있는데, 서브샘플링이 적용된 것이 그 원인 중 하나입니다.</aside>

<aside class="note"><b>알아두기:</b> 서브샘플링 관련 이야기에서는 [YCbCr](https://en.wikipedia.org/wiki/YCbCr)이라는 용어가 자주 언급됩니다. 이 용어는 색 모델의 일종인데, 감마 보정된 [RGB](https://en.wikipedia.org/wiki/RGB_color_model) 색 공간을 나타낼 수 있는 모델입니다. Y는 감마 보정이 된 밝기, Cb는 청색의 채도 성분, Cr은 적색의 채도 성분입니다. EXIF(교환 이미지 파일 형식) 데이터를 보면 샘플링 레벨 옆에 YCbCr이 표시됩니다.</aside>

채도 서브샘플링에 대해 더 알아보고 싶다면, [채도 서브샘플링 사용해 보시는 건 어때요? (Why aren’t your images using Chroma subsampling?)](https://calendar.perfplanet.com/2015/why-arent-your-images-using-chroma-subsampling/) 글을 읽어보세요.

### <a id="how-far-have-we-come-from-the-jpeg" href="#how-far-have-we-come-from-the-jpeg">기존의 JPEG에서 얼마나 발전한 상태인가요?</a>

**이 장에서는 웹 이미지 포맷의 현재 발전 상황에 대해 알아봅니다.**

*간단 요약 - 너무 파편화되어 있습니다. 최신 포맷을 채택한다면 브라우저마다 포맷 대응을 따로따로 해야하는 경우가 자주 발생합니다.*

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/format-comparison.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/format-comparison.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_95/v1502426282/essential-image-optimization/format-comparison.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_95/v1502426282/essential-image-optimization/format-comparison.jpg"
        alt="품질에 따른 최신 이미지 포맷 비교." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_95/v1502426282/essential-image-optimization/format-comparison.jpg"/>
</noscript>
</picture>
<figcaption>최신 이미지 포맷(그리고 최적화 툴)으로 이미지 크기를 26KB로 변환한 결과를 알아보기 위해 테스트를 진행했습니다. 품질은 [SSIM](https://en.wikipedia.org/wiki/Structural_similarity) (structural similarity, 구조적 유사성)과 [Butteraugli](https://github.com/google/butteraugli)를 사용해 비교해 보았습니다. 이 두 가지 측정기준에 대해서는 뒷부분에서 다루도록 하겠습니다.</figcaption>
</figure>

*   **[JPEG 2000](https://en.wikipedia.org/wiki/JPEG_2000) (2000)** – 변환 기법 기반을 이산 코사인에서 웨이블릿으로 바꿔서 기존 JPEG을 개선한 포맷입니다. **브라우저 지원: 사파리 데스크톱 + iOS**
*   **[JPEG XR](https://en.wikipedia.org/wiki/JPEG_XR) (2009)** – [HDR](http://wikivisually.com/wiki/High_dynamic_range_imaging)과 넓은 색 공간(wide [gamut](http://wikivisually.com/wiki/Gamut))을 지원하는 포맷으로, JPEG과 JPEG 2000의 대안으로 등장했습니다. JPEG보다 파일 크기는 작으나 인코딩/디코딩 속도가 살짝 느립니다. **브라우저 지원: IE Edge**
*   **[WebP](https://en.wikipedia.org/wiki/WebP) (2010)** – 구글이 만든 포맷으로, 블록예측(block-prediction)을 기반으로 합니다. 무손실과 손실 압축 둘 다 지원합니다. JPEG의 장점인 높은 이미지 크기 절약도와 PNG 파일의 용량이 큼에도 불구하고 사용했던 이유 중 하나인 투명도 지원을 모두 합니다. 채도 서브샘플링과 점진적 로딩 설정은 할 수 없습니다. JPEG 보다 디코딩 시간이 더 걸립니다. **브라우저 지원: 크롬, 오페라. 사파리와 파이어폭스에서는 실험 기능.**
*   **[FLIF](https://en.wikipedia.org/wiki/Free_Lossless_Image_Format) (2015)** – 압축률 면에서 PNG, 무손실 WebP, 무손실 BPG, 무손실 JPEG 2000을 뛰어넘는 성능을 자랑하는 무손실 이미지 포맷. **브라우저 지원: 없음. *[브라우저 내에서 사용할 수 있는 JS 디코더](https://github.com/UprootLabs/poly-flif)가 있습니다.***
*   **HEIF and BPG** - 압축 면에서 보면 두 포맷이 같지만, 외부 포장재가 다릅니다.
*   **[BPG](https://en.wikipedia.org/wiki/Better_Portable_Graphics) (2015)** – HEVC([High Efficiency Video Coding](http://wikivisually.com/wiki/High_Efficiency_Video_Coding))을 기반으로 하여 JPEG보다 압축 효율이 더 좋은 대체재가 되고자 하는 포맷입니다. MozJPEG과 WebP 보다 파일 크기를 더 잘 줄이는 것 같습니다. 라이선스 문제 때문에 사용률이 증가하는 데는 조금 문제가 있어 보입니다. **브라우저 지원: 없음. *[브라우저 안에서 사용할 수 있는 JS 디코더](https://bellard.org/bpg/)가 있습니다.***
*   **[HEIF](https://en.wikipedia.org/wiki/High_Efficiency_Image_File_Format) (2015)** – 제한적 중간 예측(constrained inter-prediction)이 적용된 HEVC 인코딩 이미지 및 이미지 시퀀스를 저장하기 위한 포맷입니다. Apple은 [WWDC](https://www.cnet.com/news/apple-ios-boosts-heif-photos-over-jpeg-wwdc/)에서 파일 크기를 반절로 줄일 수 있다는 근거를 들면서, iOS의 JPEG을 HEIF로 대체할 계획이라 발표했습니다. **브라우저 지원: 작성 시점에서는 전무. 추후 사파리 데스크톱과 iOS 11에서 지원 예정.**

눈으로 직접 포맷간의 차이를 보고 싶으시다면 이 [두 가지](http://xooyoozoo.github.io/yolo-octo-bugfixes/#cologne-cathedral&jpg=s&webp=s) 툴 중 [하나](https://people.xiph.org/~xiphmont/demo/daala/update1-tool2b.shtml)를 사용해 포맷 비교에 활용하시면 될 듯 합니다.

자, **브라우저 지원이 파편화 되어 있다는 것을 확인했으니,** 이제 만약 위 포맷 중 하나를 사용하고 싶으시다면 서비스 대상에 속하는 브라우저마다 필요하게 될 차선책 포맷을 준비해 두셔야 할 듯 합니다. 구글에서는 WebP 포맷 개발에 집중하고 있으므로 이에 대해서 곧 알아보도록 하겠습니다.

브라우저는 이미지를 렌더링 할 때 미디어 타입을 정해서 넣을 수 있으므로, 최신 이미지 포맷들(예: WebP, JPEG 2000)은 .jpg 확장자(꼭 jpg가 아니여도 됩니다)로도 저장할 수 있습니다. 덕분에 HTML을 전혀 변경할 필요 없이 이미지 전송이 가능하도록 서버쪽 [content-type 협상](https://www.igvita.com/2012/12/18/deploying-new-image-formats-on-the-web/)을 할 수 있습니다. Instart Logic과 같은 서비스에서는 사용자에게 이미지 전송시 이 방법을 씁니다.

이제 브라우저마다 이미지 포맷을 다르게 전송할 수 없을 때 택할 수 있는 다른 방법에 대해 알아보도록 하겠습니다. **최적화 JPEG 인코더**를 사용하면 됩니다.

### <a id="optimizing-jpeg-encoders" href="#optimizing-jpeg-encoders">최적화 JPEG 인코더</a>

최신 JPEG 인코더는 기존 브라우저 및 이미지 처리 앱과의 호환성을 유지하면서, 크기는 더 작은 고성능 JPEG 파일을 제작하려고 합니다. 굳이 새로운 이미지 포맷이나 변경사항을 도입하지 않고서도 압축을 더 효율적으로 하려고 하는 인코더도 있습니다. MozJPEG과 Guetzli가 여기에 속합니다.

***간단 요약: 최적화 JPEG 인코더 중 어떤 것을 사용해야 할까요?***

* 일반 웹 이미지 에셋: MozJPEG
* 품질이 주요 고려 대상이고 인코딩 시간이 길어도 상관없다면: Guetzli를 사용하세요.
* 옵션 변경을 하고 싶다면:
 * [JPEGRecompress](https://github.com/danielgtaylor/jpeg-archive) (안을 들여다보면 MozJPEG을 사용하고 있습니다)
 * [JPEGMini](http://www.jpegmini.com/). Guetzli와 유사하게 자동으로 최상 품질로 인코딩해줍니다. Guetzli처럼 기술적으로 정교하지는 않으나 속도가 더 빠릅니다. 품질이 웹용으로 더 적합한 이미지 제작을 목표로 합니다.
 * [ImageOptim API](https://imageoptim.com/api) (무료 온라인 버전은 [여기서](https://imageoptim.com/online) 사용할 수 있습니다.) –  독자적인 방식으로 색상을 다룹니다. 전체 품질과는 별개로 색상 품질을 따로 선택할 수 있습니다. 스크린샷 이미지의 경우에는 고해상도 색상을 보존하기 위해 자동으로 채도 서브샘플링 레벨을 선택해 줍니다. 그러나 일반 사진을 인코딩하는 경우 부드러운 색상을 살리기 위해 쓸데없이 용량을 낭비하는 일은 없습니다.

### <a id="what-is-mozjpeg" href="#what-is-mozjpeg">MozJPEG은 뭐죠?</a>

모질라에서는 [MozJPEG](https://github.com/mozilla/mozjpeg)이라는 최신 JPEG 인코더를 제공합니다. 모질라 측에 [의하면](https://research.mozilla.org/2014/03/05/introducing-the-mozjpeg-project/), 이 인코더를 사용하면 JPEG 파일 크기의 10%가량을 줄일 수 있다고 합니다. MozJPEG으로 압축한 파일은 모든 브라우저에서 같게 보입니다. 또한, 혁신적인 스캔 최적화 기법, [격자 구조 양자화](https://en.wikipedia.org/wiki/Trellis_quantization)(trellis quantization, 최소한으로 압축하기 위해 세부 정보를 버림), 그리고 부드러운 고 DPI 이미지를 만들 때 사용할 수 있는 상당히 괜찮은 [양자화 테이블 프리셋](https://calendar.perfplanet.com/2014/mozjpeg-3-0/)을 포함해 여러 가지 기능을 제공합니다. (ImageMagick을 사용하셔도 가능한 일이기는 합니다. 만약 여러분이 지루한 XML 세팅 작업을 견뎌낼 의지만 있다면 말이죠)

MozJPEG은 [ImageOptim](https://github.com/ImageOptim/ImageOptim/issues/45)에서도 사용이 가능합니다. 사용자 설정이 가능하며, 믿고 사용할 만한 이미지 압축 플러그인인 [imagemin plugin](https://github.com/imagemin/imagemin-mozjpeg)도 있습니다. 아래의 예시 코드에서는 Gulp를 사용하여 구현을 해보았습니다.

```js
const gulp = require('gulp');
const imagemin = require('gulp-imagemin');
const imageminMozjpeg = require('imagemin-mozjpeg');

gulp.task('mozjpeg', () =>
    gulp.src('src/*.jpg')
    .pipe(imagemin([imageminMozjpeg({
        quality: 85
    })]))
    .pipe(gulp.dest('dist'))
);
```

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image10.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image10.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image10.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image10.jpg"
        alt="커맨드라인에서 돌아가고 있는 mozJPEG" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image10.jpg"/>
</noscript>
</picture>
</figure>

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image11.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image11.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image11.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image11.jpg"
        alt="품질을 다르게 해 보았을 때 mozjpeg으로 압축한 결과. q=90에서는, 841KB. q=85에서는, 562KB. q=75에서는, 324KB. Butteraugli와 SSIM 점수는 품질을 낮출수록 조금씩 낮아진다." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image11.jpg"/>
</noscript>
</picture>
<figcaption>MozJPEG: 파일 품질에 따른 크기 및 시각적 유사성 점수 비교.</figcaption>
</figure>

저는 [jpeg-archive](https://github.com/danielgtaylor/jpeg-archive) 프로젝트의 일환인 [jpeg-compress](https://github.com/imagemin/imagemin-jpeg-recompress)를 사용하여 원본 이미지 대비 SSIM(구조적 유사성, The Structural Similarity) 점수를 계산해 보았습니다. SSIM은 두 이미지 사이의 유사성을 측정하려는 방법인데, SSIM 점수를 사용하면 하나의 이미지가 "완벽"하다는 가정하에 비교군에 속하는 이미지가 대조군과 얼마나 품질이 유사한지 측정할 수 있습니다.

제 경험상, MozJPEG은 웹용 이미지의 파일 크기를 줄이면서 품질은 높게 유지하려고 할 때 압축 옵션으로 선택하기 좋습니다. 크기가 작은 것부터 중간까지의 이미지를 대상으로 사용해 보았을 때, MozJPEG(80~85 품질로 놓았음) 덕분에 SSIM 점수는 허용치 내로 유지하면서 파일 크기를 30~40%가량 줄일 수 있었습니다. 그래서 jpeg-turbo에서 5~6%가량 성능 개선을 할 수 있었습니다. 순차적 JPEG 압축보다 [인코딩 속도는 느리지만](http://www.libjpeg-turbo.org/About/Mozjpeg), 이것 때문에 걸리적거린다는 생각이 들지는 않을 것입니다.

<aside class="note"><b>알아두기:</b> 이미지 비교 작업 설정을 조금 더 세세하게 하고 싶고, 몇 가지 유틸리티를 추가로 도입하고, MozJPEG을 지원하는 툴이 필요하다면 [jpeg-recompress](https://github.com/danielgtaylor/jpeg-archive)를 한번 사용해 보세요. 웹 성능 실전(Web Performance in Action)의 저자인 Jeremy Wagner는 설정을 [이런 식으로](https://twitter.com/malchata/status/884836650563579904) 해서 jpeg-compress를 사용해 성공적인 결과를 내놓을 수 있었습니다.</aside>

### <a id="what-is-guetzli" href="#what-is-guetzli">Guetzli는 뭐죠?</a>

[Guetzli](https://github.com/google/guetzli)는 구글에서 만든 (좀 느리기는 하지만) 전도유망하면서 지각력까지 갖춘 JPEG 인코더 입니다. 사람의 눈으로 원본과 비교해서 보았을 때 아무런 차이가 없으면서 파일 용량을 최소한으로 줄인 JPEG 이미지를 만드는 것을 목표로 합니다. 일련의 실험을 거쳐서 실험마다 최종 JPEG 파일에 대한 제안서를 하나씩 결과로 내놓는데, 각각의 제안서에는 제안의 근거가 되는 시각 심리적인 오류에 대한 설명이 담겨 있습니다. 이 제안서들을 취합한 후, 가장 높은 점수를 가진 제안서를 채택해 최종 결과물을 만들어 냅니다.

Guetzli에서는 이미지 간의 차이를 측정하기 위해 [Butteraugli](https://github.com/google/butteraugli)를 사용합니다. 이는 사람 눈의 시각 인식에 기반을 두어 이미지 차이를 측정하는 모델입니다. (아래에 관련 설명이 나옵니다.) Guetzli에서는 사람 눈의 특성을 몇가지 고려해서 계산 과정에 반영합니다. 다른 JPEG 인코더에는 없는 특징입니다. 예를 들어, 녹색광의 양과 청색광에 대한 민감도 사이의 관계를 이용하여, 녹색에 가까운 청색광에 변화가 있을 시, 이런 변경 사항들은 정확도를 조금 낮추어 인코딩하는 식입니다.

<aside class="note"><b>알아두기:</b> 이미지 파일 크기는 **코덱**보다는 **품질**에 의해 **훨씬 더** 좌지우지됩니다. 최저품질과 최고품질의 JPEG 이미지 간의 파일 크기를 비교해보면, 코덱 변경으로 인해 감량 가능한 파일 크기보다 훨씬 훨씬 더 크게 차이가 납니다. 사용할만한 품질 중에 가장 낮은 품질을 선택하는 것이 매우 중요합니다. 부주의하게 품질을 너무 높게 설정하지 마세요.</aside>

Guetzli 측에서는 같은 Butteraugli 점수를 기준으로 놓고 Guetzli를 다른 압축기와 비교를 해 보았을 때, 이미지 데이터의 크기가 20~30%가량 [줄었다고 합니다](https://research.googleblog.com/2017/03/announcing-guetzli-new-open-source-jpeg.html). 아직 Guetzli는 인코딩 속도가 무지막지하게 느리고, 정적인 콘텐츠에만 사용이 적합하다는 점을 항상 고려해서 사용해야 합니다. README 파일 내용에 따르면 Guetzli를 사용할 때는 컴퓨터 메모리가 많이 필요합니다. 메가픽셀당 1분 + 200MB RAM이 필요합니다. 실제 상황에서 Guetzli를 사용해 본 사람의 아주 좋은 체험기가 여기 이 [GitHub 스레드](https://github.com/google/guetzli/issues/50)에 올라와 있습니다. 정적 사이트 빌드 프로세스 안에서 이미지를 최적화할 때는 이상적인 옵션이지만, 그때그때 필요 때문에 이미지 최적화를 하고자 한다면 좀 힘들 겁니다.

<aside class="note"><b>알아두기:</b> Guetzli는 정적 사이트를 빌드하면서 이미지를 최적할 때, 혹은 비실시간으로 이미지를 최적화할 때에 사용하기 더 적합한 툴인듯합니다.</aside>

ImageOptim과 같은 툴(단, [최신 버전](https://imageoptim.com/))에서는 Guetzli 최적화 옵션을 제공합니다.

```js
const gulp = require('gulp');
const imagemin = require('gulp-imagemin');
const imageminGuetzli = require('imagemin-guetzli');

gulp.task('guetzli', () =>
    gulp.src('src/*.jpg')
    .pipe(imagemin([
        imageminGuetzli({
            quality: 85
        })
    ]))
    .pipe(gulp.dest('dist'))

);
```

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image12.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image12.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image12.jpg" />

<img
        class="small lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image12.jpg"
        alt="gulp 에서 최적화를 위해 guetzli를 돌려본 결과" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image12.jpg"/>
</noscript>
</picture>
</figure>

3 x 3MP 이미지를 Guetzli를 사용하여 인코딩해보면, 거의 7분가량이 (그리고 높은 CPU 사용률이) 소모됩니다. 데이터 감소량은 편차가 큽니다. Guetzli는 고화질 사진 최적화용으로 사용할 때 좋을 것 같습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image13.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image13.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image13.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image13.jpg"
        alt="파일 품질에 따른 guetzli를 사용한 결과 비교. q=100, 945KB. q=90, 687KB. q=85, 542KB." />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image13.jpg"/>
</noscript>
</picture>
<figcaption>Guetzli: 파일 품질에 따른 파일 크기 및 시각적 유사성 점수 비교.</figcaption>
</figure>

<aside class="note"><b>알아두기:</b> 고품질 이미지(예: 압축이 안 된 사용자 이미지, 품질 100%이거나 이에 가까운 고품질의 JPEG 혹은 PNG 이미지)를 최적화하는 상황이라면 Guetzli 사용을 추천합니다. 다른 이미지(예: 품질 84 이하의 JPEG 이미지)도 최적화가 되긴 하나 결과가 더 안 좋을 수도 있습니다.</aside>

Guetzli를 사용해 이미지를 압축하게 되면 매우 (매우) 시간이 오래 걸리고, 여러분 컴퓨터의 팬도 돌아가게 되지만, 큰 이미지를 압축할 필요가 있다면 사용할 만 합니다. Guetzli를 사용해 이미지의 시각적 정확도는 유지하면서 크기만 40%가량을 줄인 사례를 이미 몇 가지 보았습니다. 사진 보관용으로 딱 좋습니다. 이미지 파일 크기가 작거나 중간 정도라면 10~15KB 내외로 크기가 줄어들기는 하나 눈에 띌 정도로 좋은 결과물은 아닙니다. 이미지 크기가 작으면 압축하는 동안 액화성의 왜곡(liquify-esque distortion)이 생길 수 있습니다.

에릭 포르티스(Eric Portis)는 Guetzli를 Cloudinary의 자동 압축기술과 [비교](https://cloudinary.com/blog/a_closer_look_at_guetzli) 하여, 데이터 기준점을 몇 개 두고 이에 대한 효율 차이를 비교 조사했는데, 궁금하시다면 한번 읽어보세요.

### <a id="mozjpeg-vs-guetzli" href="#mozjpeg-vs-guetzli">MozJPEG과 Guetzli를 비교해 본다면?</a>

JPEG 인코더끼리 성능을 비교하는 작업은 복잡합니다. 압축된 이미지의 품질과 정확도는 물론 최종 파일 크기까지 비교해야 합니다. 이미지 압축 전문가인 Kornel Lesiński가 말한 것처럼, 이러한 측면을 전부가 아닌 하나만 조사한다면 [부정확한](https://kornel.ski/faircomparison) 결론을 도출할 수도 있습니다.

Guetzli과 MozJPEG를 비교해 본다면 어떤 결과가 나올까요? Kornel에 의하면 다음과 같습니다.

* Guetzli는 고품질 이미지에 맞추어져 있습니다. (Butteraugli는 `q=90+`에 가장 적합하며, MozJPEG는 `q=75` 정도가 가장 적합합니다.)
* Guetzli는 압축 속도가 훨씬 느립니다. (두 방식 모두 표준 JPEG를 생성하므로, 디코딩 속도는 평소처럼 빠릅니다)
* MozJPEG는 자동으로 품질을 설정하지 않습니다. 하지만 [jpeg-archive](https://github.com/danielgtaylor/jpeg-archive)과 같은 외부 툴을 사용하여 최적의 품질을 찾을 수 있습니다.

압축된 이미지가 외적으로, 혹은 눈으로 인식하기에 원본과 유사한지 판단하는 방법은 여러 가지가 있습니다. 이미지 품질 연구에는 [SSIM(structural similarity)](https://en.wikipedia.org/wiki/Structural_similarity) (structural similarity) 같은 방법이 주로 사용됩니다. 그러나 Guetzli는 Butteraugli에 맞추어 최적화 작업을 진행합니다.

### <a id="butteraugli" href="#butteraugli">Butteraugli</a>

[Butteraugli](https://github.com/google/butteraugli)는 Google의 프로젝트인데, 원본과 비교해 압축된 이미지의 품질 저하를 사람이 눈으로 보고 알아차리게 되는 시점을 측정하는 (원본과 비교 이미지 사이의 심리 시각적 유사성을 측정하는) 프로젝트입니다. 차이를 거의 알아차리기 어려운 이미지가 신뢰성이 있는 것으로 판단하여 높은 점수를 부여합니다. Butteraugli는 점수만 수치로 제공하는 것이 아니라, 차이 레벨의 공간 지도(spatial map)까지 계산합니다. SSIM은 이미지상의 오류를 모두 합하여 판단하는 반면에, Butteraugli는 가장 안 좋은 부분을 가지고 점수를 매깁니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image14.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image14.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_95/v1502426282/essential-image-optimization/Modern-Image14.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_95/v1502426282/essential-image-optimization/Modern-Image14.jpg"
        alt="butteraugli를 사용한 앵무새 이미지 검사" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_95/v1502426282/essential-image-optimization/Modern-Image14.jpg"/>
</noscript>
</picture>
<figcaption>위 이미지는 Butteraugli를 사용하여 적절한 JPEG 품질을 찾아낸 결과 이미지 입니다. 이 품질은 사람이 눈으로 보고 뭔가 이상하다는 것을 느끼게 되는 한계점 직전의 최소 JPEG 품질입니다. 파일 크기가 총 65% 줄어들었습니다.</figcaption>
</figure>

실제로 여러분이 작업할 때 거치는 단계는 다음과 같습니다. 목표 이미지 품질을 설정한 후 여러 가지 다양한 이미지 최적화 전략을 적용해 본 다음, Butteraugli 점수를 참조하여 파일 크기와 품질 간의 가장 좋은 균형점을 찾습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image15.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image15.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image15.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image15.jpg"
        alt="커맨드라인에서 실행 중인 butteraugli" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image15.jpg"/>
</noscript>
</picture>
<figcaption>Bezel 설치 후에 Butteraugli를 로컬에 설치하고 C++ 소스 코드로 맥에서 컴파일하기까지 30분 정도가 걸렸습니다. Butteraugli 사용방법은 꽤 간단합니다. 비교할 이미지 두 개(원본과 압축 버전)를 써주면 결과 점수를 화면에 표시해 줍니다.</figcaption>
</figure>

**Butteraugli와 그 외 다른 시각 유사성 비교법과의 차이점은 무엇인가요?**

Guetzli 프로젝트에 참여한 어떤 분의 [코멘트](https://github.com/google/guetzli/issues/10#issuecomment-276295265)에 따르면, Guetzli는 Butteraugli에서 가장 높은 점수를 얻었지만 SSIM 결과는 가장 낮았습니다. 그리고 MozJPEG은 Butteraugli와 SSIM에서 모두 같은 점수를 얻었습니다. 이 결과는 이미지 최적화 전략에 관한 제 나름의 연구 결과와 일치합니다. 제가 행한 연구는 Butteraugli와 [img-ssim](https://www.npmjs.com/package/img-ssim) 같은 노드 모듈을 실행해 Guetzli와 MozJPEG 실행 전/후의 이미지 SSIM 점수를 비교해 본 것이었습니다.

**인코더를 조합해서 써도 될까요?**

상대적으로 크기가 큰 이미지에서는 MozJPEG(jpegtran이여야 합니다. cjpeg은 Guetzli의 결과물을 지우기 때문에 안됩니다)에서 **무손실 압축** Guetzli를 결합하여 10~15% 정도(전체적으로는 55%) 용량을 줄일 수 있다는 것을 발견하였습니다. SSIM 점수는 아주 조금만 떨어뜨리면서 말이죠. 이 방법은 추가적으로 실험과 분석을 할 필요가 있습니다. [Ariya Hidayat](https://ariya.io/2017/03/squeezing-jpeg-images-with-guetzli)처럼 이 분야에 몸담고 있는 사람들 역시 실험을 진행해봤다고 하는데, 다행히 결과가 희망적이라고 하네요.

 MozJPEG는 초심자가 사용하기 쉬운 웹 에셋 인코더입니다. 비교적 인코딩 속도가 빠르며 양질의 이미지를 생성해 냅니다. Guetzli는 리소스가 많은 경우, 그리고 이미지가 많고 품질이 높을 수록 잘 작동하므로 저라면 중고급 사용자를 위해 이 선택지를 남겨두겠습니다.

## <a id="what-is-webp" href="#what-is-webp">WebP가 뭔가요?</a>

[WebP](https://developers.google.com/speed/webp/)는 Google에서 만든 최신 이미지 포맷입니다. 품질은 일정 수치 이상으로 유지하면서, 손실/비손실 모드에서 모두 조금이라도 더 파일 크기를 줄여보는 것을 목표로 합니다. 알파 채널 투명도와 애니메이션을 둘 다 지원합니다.

작년에 WebP는 손실 및 무손실 압축 모드 성능이 둘 다 몇 퍼센트 가량 향상되었고, 속도는 두 배 이상 빨라져서 압축 해제 성능 역시 10% 정도 향상되었습니다. WebP는 모든 상황에서 사용할 만한 툴은 아니지만, 이미지 압축 커뮤니티 내에서는 단단한 지지기반과 계속해서 늘어나는 사용자수를 보유중입니다. 그 이유를 알아보도록 하겠습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image16.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image16.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image16.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image16.jpg"
        alt="파일 품질 세팅에 따른 webp 비교. q=90, 646KB. q=80= 290KB. q=75, 219KB. q=70, 199KB" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/Modern-Image16.jpg"/>
</noscript>
</picture>
<figcaption>WebP: 파일 품질에 따른 파일 크기 및 시각적 유사성 점수 비교.</figcaption>
</figure>

### <a id="how-does-webp-perform" href="#how-does-webp-perform">WebP의 성능은 어떤가요?</a>

**손실 압축**

WebP 손실 압축 파일은 VP8, VP9 비디오 키 프레임 인코딩 방식과 비슷한 방식으로 압축된 것인데, WebP 팀에 의하면 평균적으로 JPEG 파일보다 [25-34%](https://developers.google.com/speed/webp/docs/webp_study)가량 파일 크기가 작다고 합니다.

저품질 범위(0-50) 내에서는 보기 안 좋은 블록 아티팩트를 제거할 수 있으므로 JPEG보다 WebP가 더 유리합니다. WebP 중간 품질(-m 4 -q 75)은 속도와 파일 크기 사이의 기본적인 균형값입니다. 더 높은 범위(80-99)에서는 WebP가 유리한 점이 줄어듭니다. WebP는 품질보다 속도가 중요한 경우에 사용을 권장합니다.

**무손실 압축**

[WebP 무손실 압축 파일은 PNG 파일보다 26% 정도 크기가 작습니다](https://developers.google.com/speed/webp/docs/webp_lossless_alpha_study). PNG와 비교하면 로딩 시간이 무손실의 경우 3%가량 적게 듭니다. 그렇다고 해도 보통 사용자들에게 무손실 이미지를 전달하는 경우는 많지 않을 겁니다. 무손실이라고 가장자리가 선명하게 처리되는 것은 아닙니다. (예: 비-JPEG) 무손실 WebP는 콘텐츠 저장용으로 적합할 것 같습니다.

**투명도**

WebP에는 무손실 8bit 투명 채널이 포함되어 있는데, PNG보다 바이트 수가 22%밖에 많지 않습니다. 손실 RGB 투명도 역시 지원하는데 이 기능은 WebP에만 존재합니다.

**메타데이터**

WebP 파일 포맷에서는 EXIF 사진 메타데이터와 XMP 디지털 문서 메타데이터도 사용할 수 있습니다. ICC 색상 프로필도 지원합니다.

WebP는 CPU 의존도가 높아진 만큼 압축률이 높습니다. 2013년의 WebP 압축 속도는 JPEG보다 최대 10배 느렸었는데, 이제는 과거의 이야기일 뿐입니다. (일부 이미지의 경우 아직 2배 정도 느리긴 할 겁니다) 빌드 과정 내에서 처리되는 정적 이미지의 경우에는 WebP를 사용해도 큰 문제가 되지 않습니다. 동적으로 이미지를 생성한다면 눈에 띌 정도로 CPU 오버헤드가 발생할 수도 있습니다. 따라서 이에 대한 측정을 시험 삼아 미리 해볼 필요가 있습니다.

<aside class="note"><b>알아두기:</b> WebP 손실 압축 품질 설정은 JPEG의 경우와 직접적인 비교가 불가능합니다. JPEG의 ‘품질 70%’는 WebP의 ‘품질 70%’와 꽤 다르게 보일 것입니다. WebP 포맷에서는 데이터를 더 많이 버려서 파일 크기를 줄이려고 하기 때문입니다.</aside>

### <a id="whos-using-webp-in-production" href="#whos-using-webp-in-production">WebP는 실제로 어디서 사용 중인가요?</a>

다수의 대기업에서는 비용 절감과 웹페이지 로딩 시간 축소를 위해 WebP를 실제로 사용하고 있습니다.

Google에 따르면 다른 손실 압축 방식 말고 WebP를 사용했을 때의 비용을 30~35% 정도 줄일 수 있으며, 하루에 430억 회의 이미지 요청을 처리할 수 있었고, 그중의 26% 정도가 무손실 압축 파일 요청이었다고 합니다. 만약 [브라우저 지원 상황](http://caniuse.com/#search=webp)이 개선되고 WebP가 더 많이 사용된다면 비용 절감 효과는 의심의 여지 없이 더 커지게 될 것입니다. Google Play나 YouTube 같은 실제 서비스 사이트에서도 WebP를 사용하고 있다고 합니다.

Netflix, Amazon, Quora, Yahoo, Walmart, Ebay, The Guardian, Fortune, USA Today 사이트 모두 WebP 지원 범위 내에서는 이 포맷으로 이미지를 압축하여 전달합니다. VoxMedia에서는 크롬 사용자들에게는 WebP 이미지를 전달함으로써 The Verge 로딩 시간을 [1~3초 정도 줄이는 데](https://product.voxmedia.com/2015/8/13/9143805/performance-update-2-electric-boogaloo) 성공했다고 합니다. [500px](https://iso.500px.com/500px-color-profiles-file-formats-and-you/)에서도 역시 크롬 브라우저 사용자들에게 WebP를 제공하는 것으로 방침을 바꿈으로써 이미지 품질은 예전과 비슷하거나 더 좋은 품질로 제공하면서 평균 파일 크기는 25% 정도 줄어들었다고 합니다.

여기서 예시로 든 사이트 말고도 WebP를 사용중인 사이트가 몇 군데 더 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/webp-conversion.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/webp-conversion.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/webp-conversion.jpg" />

<img
        class="small lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/webp-conversion.jpg"
        alt="Google의 WebP 관련 수치들: 하루에 이미지 요청이 430억 회 이상" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/webp-conversion.jpg"/>
</noscript>
</picture>
<figcaption>Google의 WebP 사용현황: YouTube, Google Play, Chrome Data Saver, G+ 통틀어 하루에 430억 회가량 WeP 이미지 요청이 들어옵니다.</figcaption>
</figure>

### <a id="how-does-webp-encoding-work" href="#how-does-webp-encoding-work">WebP 인코딩 원리는 뭔가요?</a>

WebP의 손실 인코딩은 스틸 이미지에서 JPEG 포맷에 필적하게끔 고안되었습니다. WebP 손실 인코딩은 다음 세 가지 특징을 지닙니다.

**매크로블록(Macro-blocking)** – 이미지를 16x16개의 (매크로) 밝기 픽셀 블록과 8x8 개의 채도 픽셀 두 세트로 나눕니다. JPEG에서 사용되는 색 공간 전환 기법, 채도 채널 다운샘플링과 이미지 세분화(subdivision) 개념을 알고 있다면 뭔가 친숙해 보일 겁니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image18.png"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image18.png"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image18.png" />

<img
        class="small lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image18.png"
        alt="Google Doodle 매크로블록 예시. 일련의 이미지 픽셀을 밝기와 채도 블록으로 세분화했다."/>
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image18.png"/>
</noscript>
</picture>

</figure>

**예측(Prediction)** – 매크로블록 안의 모든 서브 블록(4x4개)에는 필터링을 효율적으로 하기 위한 예측 모델이 적용되어 있습니다. 예측 모델을 적용하려면 블록 주변의 픽셀을 두 세트로 일단 뽑아야 합니다. 하나는 A 세트(픽셀 바로 위의 행), 다른 하나는 L 세트(픽셀 왼쪽의 열)입니다. 이 두 픽셀 세트를 가지고 인코더는 4x4 서브블록의 테스트 블록을 채운 다음에, 원본 블록과 가장 가까운 값을 가려냅니다. Colt McAnlis의 글 [WebP 손실 압축 모드 원리(How WebP lossy mode works)](https://medium.com/@duhroach/how-webp-works-lossly-mode-33bd2b1d0670)에 이에 대한 더 자세한 설명이 실려 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image19.png"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image19.png"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image19.png" />

<img
        class="lazyload very-small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image19.png"
        alt="예측 모델 선정에 사용되는 목표 블록과 샘플이 될 행과 열을 표시한 Google Doodle 예시."/>
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image19.png"/>
</noscript>
</picture>

</figure>

JPEG 인코딩처럼 이산 코사인 변환(DCT, Discrete Cosine Transform)이 몇 단계에 걸쳐 적용되었습니다. 가장 핵심적인 차이는 바로 JPEG에서는 Huffman 압축을 하지만, WebP에서는 [산술 압축기(Arithmetic Compressor)](https://www.youtube.com/watch?v=FdMoL3PzmSA&index=7&list=PLOU2XLYxmsIJGErt5rrCqaSGTMyyqNt2H)를 쓴다는 것입니다.

더 자세히 알아보고 싶다면, Google Developer에 실린 글 중 [WebP 압축 기법(WebP Compression Techniques)](https://developers.google.com/speed/webp/docs/compression)에 자세히 설명되어 있으니 참조하세요.

### <a id="webp-browser-support" href="#webp-browser-support">WebP 브라우저 지원</a>

모든 브라우저에서 WebP를 지원하지는 않지만 [CanIUse.com에 따르면](http://caniuse.com/webp), 전 세계 실사용 브라우저 중 약 74% 정도에서 지원 중이라고 합니다. 크롬과 오페라 브라우저는 WebP 지원을 기본으로 합니다. 사파리, 엣지, 파이어폭스에서는 아직은 개발자 버전에만 WebP가 지원되고, 정식 배포 버전에서는 지원되지 않습니다. 따라서 WebP 이미지를 사용자에게 보여주는 일은 종종 웹 개발자가 책임지게 됩니다. 이에 대해서는 나중에 이야기하겠습니다.

주요 브라우저별 WebP 지원 버전 목록은 다음과 같습니다.

* 크롬: 버전 23부터 완벽하게 지원하기 시작했습니다.
* 안드로이드 크롬: 크롬 50 부터
* 안드로이드: 4.2부터
* 오페라: 12.1 부터
* 오페라 미니: 모든 버전 지원
* 파이어폭스: 몇몇 베타 버전에서 지원
* 엣지: 몇몇 베타 버전에서 지원
* 인터넷 익스플로러: 지원 안 함
* 사파리: 몇몇 베타 버전에서 지원

WebP라고 해서 단점이 없는 것은 아닙니다. 색 공간을 전체 해상도(full-resolution)로 지정할 수 있는 옵션이 없고, 점진적 디코딩도 지원하지 않습니다. 다르게 말하자면, WebP 관련 툴들은 사용하기에 꽤 괜찮고 브라우저 지원도 양호하나, 이 글의 작성 시점에서 볼 때 앞의 두 장점은 크롬과 오페라 브라우저의 경우에만 해당되는 이야기입니다. 만약에 여러분이 서비스 사용자 그룹을 이 두 브라우저 사용자만을 대상으로 해도 충분하다면 차선책을 마련해놓고 WebP를 사용해볼 만 합니다.

### <a id="how-do-i-convert-to-webp" href="#how-do-i-convert-to-webp">이미지를 어떻게 WebP로 변환시키죠?</a>

몇 가지 상업용, 혹은 오픈 소스 이미지 에디팅/프로세싱 패키지에서는 이미지를 WebP로 변환할 수 있습니다. 이 중에 특히 유용한 애플리케이션을 꼽자면 XnConvert가 있습니다. 무료인 데다가 플랫폼을 다양하게 지원하며, 영상 처리 변환을 일괄적으로 할 수 있습니다.

<aside class="note"><b>알아두기:</b> 품질이 떨어지거나 평균 정도인 JPEG 이미지는 되도록 WebP로 변환하지 마세요. 이는 흔히 저지르는 실수인데, 변환하게 되면 WebP 이미지에 JPEG 압축에 의한 아티팩트가 생기게 됩니다. 이미지가 저장된 _후에_ JPEG으로 인한 왜곡이 추가되기 때문에 이미지 품질 손실이 두 번이나 일어나는 셈입니다. 따라서 WebP 포맷의 효율성이 떨어지게 됩니다. 애플리케이션에 변환하려고 넣는 파일은 할 수 있는 한 최상의 품질로, 그리고 가능하다면 초기 원본으로 넣어 주세요.</aside>

**[XnConvert](http://www.xnview.com/en/xnconvert/)**

XnConvert를 사용하면 한꺼번에 많은 양의 영상을 처리할 수 있으면서, 500가지 이상의 포맷을 전부 대응할 수 있습니다. 80가지 이상의 개별 액션을 조합해서 다양한 방식으로 이미지를 변환하고 편집할 수 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image20.png"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image20.png"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image20.png" />

<img
        class="small lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image20.png"
        alt="Mac에서 XnConvert App을 사용해 이미지 여러 장을 변환 중."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image20.png"/>
</noscript>
</picture>
<figcaption>XnConvert에서는 이미지 일괄 최적화 기능을 제공하므로, 원본 파일을 WebP나 다른 포맷으로 간단하게 변환할 수 있습니다. XnConvert에서는 이미지 압축뿐만 아니라 메타데이터 제거, 이미지 크로핑, 색심도(color depth) 최적화 및 기타 변환 작업을 할 수 있습니다.</figcaption>
</figure>

xnview 웹사이트에 따르면 애플리케이션 옵션에는 다음과 같은 것들이 있습니다.

*   메타데이터: 편집
*   변환: 회전, 자르기, 크기 변환
*   조정: 밝기, 대비, 채도
*   필터: 블러, 볼록하게(Emboss), 선명하게(Sharpen)
*   효과: 마스킹, 워터마크, 비네팅

옵션을 조절해보면 WebP를 포함해 70가지 이상의 다양한 포맷으로 파일을 내보낼 수 있습니다. XnConvert는 리눅스, 맥, 윈도우 운영체제에서 무료로 사용할 수 있습니다. XnConvert는 사용 추천을 많이 받고 있으며, 특히 소규모 회사를 대상으로 추천이 많이 됩니다.

**노드 모듈**

[Imagemin](https://github.com/imagemin/imagemin)은 유명한 이미지 압축 모듈입니다. WebP 포맷으로 이미지를 변환할 수 있는 플러그인([imagemin-webp](https://github.com/imagemin/imagemin-webp))까지 제공합니다. 이 플러그인에서는 손실과 무손실 압축 둘 다 지원합니다.

imagemin과 imagemin-webp 모듈을 설치하려면 다음 명령어를 입력하세요.

```
> npm install --save imagemin imagemin-webp
```

그 후 모듈을 `require()`로 불러와서 포맷에 상관없이 프로젝트 폴더 내에 존재하는 아무 이미지(JPEG이든 뭐든)에 적용해 최적화를 진행하면 됩니다. 아래는 60 품질로 WebP 인코더를 사용해 손실 인코딩을 하는 코드입니다.

```js
const imagemin = require('imagemin');
const imageminWebp = require('imagemin-webp');

imagemin(['images/*.{jpg}'], 'images', {
    use: [
        imageminWebp({quality: 60})
    ]
}).then(() => {
    console.log(‘Images optimized’);
});
```

JPEG처럼 WebP 역시 압축 후에 이미지에 부자연스러운 부분이 눈에 띌 수 있습니다. 여러분의 프로젝트에 들어가는 이미지에는 어떤 품질 수치가 적당할지 찾아보세요. imagemin-webp는 무손실 품질로 WebP 이미지(24bit 색상 및 투명도 완벽 지원)를 인코딩할 때도 사용할 수 있습니다. 옵션에 `lossless: true`를 추가해 주면 됩니다.

```js
const imagemin = require('imagemin');
const imageminWebp = require('imagemin-webp');

imagemin(['images/*.{jpg,png}'], 'build/images', {
    use: [
        imageminWebp({lossless: true})
    ]
}).then(() => {
    console.log(‘Images optimized’);
});
```

또한, imagemin-webp를 토대로 하여 Sindre Sorhus가 만든 Gulp용 [WebP 플러그인](https://github.com/sindresorhus/gulp-webp)과 [WebPack용 WebP 로더](https://www.npmjs.com/package/webp-loader)도 사용해 볼 수 있습니다. Gulp 플러그인에서는 imagemin 플러그인 옵션을 전부 사용할 수 있습니다.

```js
const gulp = require('gulp');
const webp = require('gulp-webp');

gulp.task('webp', () =>
    gulp.src('src/*.jpg')
    .pipe(webp({
        quality: 80,
        preset: 'photo',
        method: 6
    }))
    .pipe(gulp.dest('dist'))
);
```

무손실 압축이라면 다음과 같이 하면 됩니다.

```js
const gulp = require('gulp');
const webp = require('gulp-webp');

gulp.task('webp-lossless', () =>
    gulp.src('src/*.jpg')
    .pipe(webp({
        lossless: true
    }))
    .pipe(gulp.dest('dist'))
);
```

**Bash를 사용하여 이미지 일괄 최적화하기**

XNConvert를 사용하면 이미지를 한꺼번에 여러 장 최적화할 수 있긴 하나, 만약에 애플리케이션이나 빌드 시스템 쓰는 것을 별로 좋아하지 않는다면, bash와 이미지 최적화 바이너리 파일을 사용하여 간편하게 최적화할 수 있습니다.

[cwebp](https://developers.google.com/speed/webp/docs/cwebp)를 사용하여 대량의 이미지를 WebP로 변환하는 명령어는 다음과 같습니다.

```
find ./ -type f -name '*.jpg' -exec cwebp -q 70 {} -o {}.webp \;
```

아니면 [jpeg-recompress](https://github.com/danielgtaylor/jpeg-archive)를 사용해 이미지를 MozJPEG으로 일괄 최적화할 수 있습니다.

```
find ./ -type f -name '*.jpg' -exec jpeg-recompress {} {} \;
```

SVG는 [svgo](https://github.com/svg/svgo)를 사용해 최적화 하면 됩니다. (이에 대해서는 뒤에 더 나옵니다.)

```
find ./ -type f -name '*.svg' -exec svgo {} \;
```

Jeremy Wagner는 ['Bash를 사용해 이미지 최적화하기'](https://jeremywagner.me/blog/bulk-image-optimization-in-bash)에서 이에 대해 더 자세히 설명합니다. 같이 쓴 [이 글](https://jeremywagner.me/blog/faster-bulk-image-optimization-in-bash) 역시 읽어 볼 만합니다.

**그 외 기타 WebP 이미지 처리 및 편집 앱**

* Leptonica: 오픈 소스 이미지 처리 및 분석 앱에 관한 총체적인 정보가 담겨 있는 웹사이트
* Sketch에서는 WebP 직접 변환 가능
    * GIMP — 포토샵 대용으로 사용 가능한 무료 오픈 소스 이미지 에디터.
    * ImageMagick — 무료로 사용 가능한 커맨드 라인 앱. 비트맵 이미지 생성, 변환, 편집 가능.
    * Pixelmator — 맥에서 사용 가능한 유료 이미지 에디터.
    * Photoshop WebP Plugin — 구글에서 만든 무료 플러그인. 이미지 불러오기 및 내보내기 가능.

**안드로이드:** 안드로이드 스튜디오에서 BMP, JPG, PNG, 혹은 정적 GIF 이미지를 WebP로 변환할 수 있습니다. 더 자세한 내용은 [안드로이드 스튜디오에서 WebP 이미지 만들기](https://developer.android.com/studio/write/convert-webp.html)를 참고하세요.

### <a id="how-do-i-view-webp-on-my-os" href="#how-do-i-view-webp-on-my-os">제 OS에서 WebP 이미지를 어떻게 열어보나요?</a>

블링크 엔진 기반의 브라우저(크롬, 오페라, 브레이브)를 사용하면 파일을 끌어다 브라우저에 놓아서 WebP 이미지를 볼 수 있고, 맥이나 윈도우 운영체제 사용자라면 플러그인을 사용해 바로 미리보기 할 수 있습니다.

몇 년 전에 [페이스북에서는 WebP를 가지고 실험](https://www.cnet.com/news/facebook-tries-googles-webp-image-format-users-squawk/)을 했는데, 그 결과 파일이 WebP 형식으로 되어있기 때문에 마우스 우클릭으로 사진을 디스크에 저장하려는 사용자들이 브라우저 밖에서 사진 열람을 못 한다는 사실을 발견했습니다. 여기서 세 가지 중요 이슈를 꼽아 볼 수 있습니다.

<ul>
<li>WebP 파일을 "다른 이름으로 저장"했으나 로컬에서 열어볼 수 없었습니다. 크롬 브라우저 자체를 ".webp" 핸들러로 등록해 둠으로써 이 문제를 해결할 수 있었습니다.</li>
<li>"다른 이름으로 저장" 후에 이미지 파일을 이메일에 첨부하여 크롬 브라우저를 사용하지 않는 사람과 공유할 때 문제가 생깁니다. 페이스북은 사이트 UI에 눈에 잘 띄는 "다운로드" 버튼을 만들어 두어서, 사용자들이 이 버튼을 클릭해 다운로드 요청 시 JPEG 파일을 받아 볼 수 있도록 함으로써 이 문제를 해결했습니다.</li>
<li>우클릭 > URL 복사 후 웹에 URL을 공유할 때 문제가 생깁니다. content-type 협상([content-type negotiation](https://www.igvita.com/2012/12/18/deploying-new-image-formats-on-the-web/))으로 이 문제를 해결했습니다.</li>
</ul>

여러분 서비스의 사용자에게는 위 이슈로 인한 영향이 별로 없을 수도 있으니, 그런 경우 그냥 가벼운 마음을 가지고 SNS 파일 공유성에 대한 흥미로운 내용 정도로 알아두시고 넘어가시면 됩니다. 고맙게도 요즘에는 다양한 OS에서 WebP 파일을 열어보고 작업할 수 있는 유틸리티들이 있습니다.

맥에서는 WebP용 [Quick Look 플러그인(qlImageSize)](https://github.com/Nyx0uf/qlImageSize)을 한번 사용해 보세요. 훌륭한 툴입니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image22.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image22.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image22.jpg" />

<img
        class="small lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image22.jpg"
        alt="WebP 파일용 Quick Look 플러그인을 사용해 데스크톱 맥에서 WebP 파일을 보고 있음."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image22.jpg"/>
</noscript>
</picture>
</figure>

윈도우에서는 [WebP 코덱 패키지](https://storage.googleapis.com/downloads.webmproject.org/releases/webp/WebpCodecSetup.exe)를 내려받을 수 있습니다. 이 패키지를 사용하면 파일 탐색기와 윈도우 포토 뷰어에서 WebP 이미지를 열어볼 수 있습니다.

### <a id="how-do-i-serve-webp" href="#how-do-i-serve-webp">WebP는 어떻게 서버에서 전송해야 하나요?</a>

WebP 지원이 없는 브라우저에서는 아무런 이미지도 나오지 않는 바람직하지 못한 경우가 발생할 수 있습니다. 이런 상황이 발생하지 않도록 브라우저의 WebP 지원 여부에 따라 파일 포맷을 선택적으로 전달하는 전략이 몇 가지 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/play-format-webp.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/play-format-webp.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/play-format-webp.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/play-format-webp.jpg"
        alt="The Chrome DevTools Network panel displaying the waterfall for the Play Store in Chrome, where WebP is served."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/play-format-webp.jpg"/>
</noscript>
</picture>
<figcaption>크롬 개발자도구 네트워크 패널을 열어서 "Type" 열을 보면 블링크 기반 브라우저에서는 WebP 파일이 전달되었습니다.</figcaption>
</figure>

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/play-format-type.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/play-format-type.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/play-format-type.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/play-format-type.jpg"
        alt="While the Play store delivers WebP to Blink, it falls back to JPEGs for browsers like Firefox."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/play-format-type.jpg"/>
</noscript>
</picture>
<figcaption>똑같은 구글 플레이 사이트에 접속해도 블링크 기반 브라우저에서는 WebP 이미지를 전달받지만, 파이어폭스 같은 브라우저를 사용하면 JPEG 이미지를 전달받습니다.</figcaption>
</figure>

서버에서 사용자에게 WebP 이미지 전달하는 방법을 소개합니다.

**.htaccess를 사용해 WebP 복사본을 전달합니다**

서버에 JPEG/PNG 파일의 .webp 버전이 존재할 때, .htaccess 파일을 사용해 WebP 형식을 지원하는 브라우저에 해당 파일을 전달하는 법을 소개하도록 하겠습니다.

Vincent Orback이 추천하는 방법은 다음과 같습니다.

브라우저는 [Accept Header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept)를 통해 [WebP 지원 상황을 명시적으로](http://vincentorback.se/blog/using-webp-images-with-htaccess/) 알릴 수 있습니다. 만약 여러분이 백엔드 쪽을 수정할 수 있는 권한을 가지고 있고, JPEG나 PNG 포맷이 아니라 WebP 이미지가 디스크에 존재하는 경우에는 WebP 버전을 반환할 수도 있습니다. 그러나 항상 가능한 방법은 아니므로 (예를 들어 GitHub 페이지나 S3과 같이 정적인 호스트 환경에서는 사용 불가능) 미리 가능 여부를 확실히 점검해 두세요.

아파치 웹 서버에서 사용하는 .htaccess 파일 샘플은 다음과 같습니다.

```
<IfModule mod_rewrite.c>

  RewriteEngine On

  # Check if browser support WebP images
  RewriteCond %{HTTP_ACCEPT} image/webp

  # Check if WebP replacement image exists
  RewriteCond %{DOCUMENT_ROOT}/$1.webp -f

  # Serve WebP image instead
  RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp,E=accept:1]

</IfModule>

<IfModule mod_headers.c>

    Header append Vary Accept env=REDIRECT_accept

</IfModule>

AddType  image/webp .webp
```

.webp 이미지가 노출될 때 문제가 좀 있다면 image/webp MIME 타입을 서버에 활성화해 놓았는지 꼭 확인해 보세요.

아파치 서버의 경우, 다음 코드를 .htaccess 파일에 추가해 주면 됩니다.

```
AddType image/webp .webp
```

Nginx의 경우는 다음과 같은 코드를 mime.types 파일에 추가합니다.

```
image/webp webp;
```

<aside class="note"><b>알아두기:</b> Vincent Orback이 참고용으로 WebP 파일 전달용 [htaccess 설정](https://github.com/vincentorback/WebP-images-with-htaccess) 샘플을 올려두었습니다. Ilya Grigorik은 [WebP 파일 전달용 설정 스크립트](https://github.com/igrigorik/webp-detect) 모음을 유지보수하고 있는데, 유용하게 쓰일 것 같네요.</aside>

**`<picture>` 태그 사용하기**

`<picture>` 태그를 사용하면 브라우저 자체에서 화면에 어떤 이미지 포맷을 보여줄지 선택할 수 있습니다. `<picture>` 태그에는 `<source>` 요소 여러개와 `<img>` 태그 하나가 들어 있습니다. 실제 브라우저에 적용 가능한 이미지 파일을 가지고 있는 `<source>` 태그가 실제 DOM 요소가 됩니다. 브라우저는 `<source>` 요소들을 차례로 순회하면서 첫 번째로 매칭되는 요소를 찾아냅니다. 만약 사용자 브라우저에서 `<picture>` 태그를 지원하지 않는다면, `<div>` 요소가 렌더링 되며 그 안에 `<img>` 요소가 들어갑니다.

<aside class="note"><b>알아두기:</b> `<source>` 요소를 사용할 때는 배치 순서에 주의하십시오. image/webp 소스를 레거시 포맷 다음에 놓지 말고, 그 앞에 두도록 하세요. WebP 포맷을 인식하는 브라우저에서는 이 소스를 사용하고, 인식하지 못하는 브라우저는 좀 더 폭넓은 지원이 가능한 포맷으로 눈을 돌릴 것입니다. 만약 이미지의 실제 크기가 모두 똑같다면 (`media` 속성을 쓰지 않는다는 가정하에) 파일 크기순대로 이미지를 놓으십시오. 파일 순서는 보통 레거시 포맷을 가장 뒤에 놓을 때와 비슷하게 배치됩니다.</aside>

HTML 예시 코드는 다음과 같습니다.

```html
<picture>
  <source srcset="/path/to/image.webp" type="image/webp">
  <img src="/path/to/image.jpg" alt="">
</picture>

<picture>   
    <source srcset='paul_irish.jxr' type='image/vnd.ms-photo'>  
    <source srcset='paul_irish.jp2' type='image/jp2'>
    <source srcset='paul_irish.webp' type='image/webp'>
    <img src='paul_irish.jpg' alt='paul'>
</picture>

<picture>
   <source srcset="photo.jxr" type="image/vnd.ms-photo">
   <source srcset="photo.jp2" type="image/jp2">
   <source srcset="photo.webp" type="image/webp">
   <img src="photo.jpg" alt="My beautiful face">
</picture>
```

**CDN에서 WebP 포맷으로 자동 변환하기**

일부 CDN에서는 WebP 포맷 자동 변환 기능을 제공합니다. 또한 WebP 사용을 [지원하는 곳이라면 어디서나](http://cloudinary.com/documentation/responsive_images#automating_responsive_images_with_client_hints) 사용할 수 있도록 클라이언트 힌트를 제공할 수 있습니다. 사용 중인 CDN의 제공 서비스 안에 WebP 지원이 포함되어 있는지 확인해 보세요. 더 손쉬운 해결책이 그 안에서 여러분을 기다리고 있을지도 모릅니다.

**WordPress WebP 지원**

**Jetpack** — 널리 쓰이는 워드프레스 플러그인인 Jetpack에는 [Photon](https://jetpack.com/support/photon/)이라 불리는 CDN 이미지 서비스가 들어있습니다. Photon을 사용하면 WebP 이미지를 버벅임없이 보여줄 수 있습니다. Jetpack은 무료 사용자도 Photon CDN 기능을 사용할 수 있으므로, 비용 면에서도 괜찮으며 기능 구현을 편하게 할 수 있습니다. 단점은 Photon이 이미지 크기를 재조정하면서 URL에 쿼리 스트링을 집어 넣기 때문에 이미지마다 추가로 DNS 룩업이 필요하다는 점입니다.

**캐시 활성기(Cache Enabler)와 최적화 실행기(Optimizer)** — 만약 워드프레스를 사용하고 계시다면, 최소한 반절 정도 오픈 소스라고 할 수 있는 옵션이 존재합니다. [Cache Enabler](https://wordpress.org/plugins/cache-enabler/)라 불리는 이 오픈 소스 플러그인은 메뉴에 WebP 이미지 캐싱 체크박스 옵션이 있어서, WebP를 지원하는 브라우저에서 옵션이 활성화되어 있다면 캐싱된 이미지를 유저에게 전달하도록 만듭니다. 덕분에 쉽게 WebP 이미지 전달을 할 수 있습니다. 그러나 단점이 있습니다. Cache Enabler를 사용하려면 Optimizer라는 일종의 형제 프로그램을 같이 사용해야 하는데, 연간 사용료를 내야 합니다. 이 점 때문에 진정한 오픈 소스 해결책이라고 부를 수가 없을 것 같네요.

**Short Pixel** — Cache Enabler와 함께 사용할 수 있는 다른 유료 옵션입니다. Short Pixel의 기능은 앞에서 설명한 Optimizer와 꽤 비슷합니다. 한달에 이미지 100장을 무료로 최적화 할 수 있습니다.

**애니메이션 GIF 압축에 대한 설명과 이보다는 `<video>`가 더 나은 이유**

애니메이션 GIF는 사용처가 매우 제한적인 포맷임에도 불구하고, 계속해서 널리 사용되고 있습니다. 소셜 네트워크 사이트 부터 유명한 미디어 사이트에 이르기까지 모든 곳에서 애니메이션 GIF를 매우 많이 사용하고 있으나, 사실 이 포맷은 비디오나 애니메이션용으로 디자인된 것이 결코 아닙니다. 실제로 [GIF89a 명세서](https://www.w3.org/Graphics/GIF/spec-gif89a.txt)를 보면 ‘GIF는 애니메이션을 위한 플랫폼 역할을 하고자 만든 것이 아닙니다’라고 써 있습니다. [색상수, 프레임 수 및 프레임 크기](http://gifbrewery.tumblr.com/post/39564982268/can-you-recommend-a-good-length-of-clip-to-keep-gifs)는 모두 애니메이션 GIF 파일 크기 증대에 영향을 줍니다. 포맷을 GIF에서 비디오로 바꾸면 크기를 제일 많이 줄일 수 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/animated-gif.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/animated-gif.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/animated-gif.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/animated-gif.jpg"
        alt="애니메이션 GIF와 비디오 파일 크기 비교: 동일한 품질이나 포맷을 다르게 했을 때"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/q_100/v1502426282/essential-image-optimization/animated-gif.jpg"/>
</noscript>
</picture>
<figcaption>애니메이션 GIF와 비디오 파일 크기 비교: 동일한 품질이나 포맷을 다르게 했을 때</figcaption>
</figure>

**똑같은 파일을 MP4 비디오 포맷으로 만들면 기존 파일 크기에서 80% 이상을 줄일 수 있습니다.** GIF는 종종 막대한 대역폭 낭비의 주범이 될 뿐만 아니라, 불러올 때도 더 오래 걸리고, 색상수도 더 적으면서, 사용성 역시 평균 이하입니다. 아마도 트위터 사이트에 업로드 된 애니메이션 GIF가 다른 웹사이트들 보다는 트위터에서 더 성능이 좋아 보인 적이 있었을 겁니다. [트위터에 올라온 애니메이션 GIF는 사실 진짜 GIF 파일이 아닙니다.](http://mashable.com/2014/06/20/twitter-gifs-mp4/#fiiFE85eQZqW) 사용성 향상과 대역폭 소비 절감을 위해서, 트위터에서는 사용자가 GIF를 업로드하면 포맷을 비디오로 전환합니다. 이와 비슷하게 [Imgur에서도 GIF 업로드시 여러분을 위해 포맷을 조용히 알아서 비디오로 변환](https://thenextweb.com/insider/2014/10/09/imgur-begins-converting-gif-uploads-mp4-videos-new-gifv-format/)합니다.

왜 GIF 파일이 크기가 몇 배는 더 큰 것일까요? 애니메이션 GIF는 각 프레임을 무손실 GIF 이미지로 저장합니다. 네, '무손실'입니다. 우리가 종종 느끼는 열화된 품질은 GIF에서 컬러 팔레트 색상을 256가지로 제한하고 있기 때문에 겪는 것입니다. H.264와 같은 비디오 코덱과는 다르게, GIF는 압축할 때 이웃한 프레임을 고려하지 않기 때문에 크기가 더 커집니다. MP4 비디오 포맷은 각 키 프레임을 손실 JPEG으로 저장합니다. 즉, 압축 효율을 높이기 위해 원본 데이터의 일부를 버리는 것이죠.

**GIF 파일을 비디오 포맷으로 바꿀려면**

*   [ffmpeg](https://www.ffmpeg.org/)을 사용해 애니메이션 GIF(혹은 소스 파일)을 H.264 MP4 포맷으로 변환하세요. 저는 [Rigor](http://rigor.com/blog/2015/12/optimizing-animated-gifs-with-html5-video)에서 짤막하게 한 문장으로 된 코드를 가져다 사용했습니다. 신선한 농담같아 보이네요. "`ffmpeg -i animated.gif -movflags faststart -pix_fmt yuv420p -vf "scale=trunc(iw/2)2:trunc(ih/2)2" video.mp4`"

*   ImageOptim API에서도 [애니메이션 GIF를 WebM/H.264 비디오로 변환](https://imageoptim.com/api/ungif)하는 기능을 지원합니다. 비디오 코덱 압축을 더 효율적으로 할 수 있도록 [GIF의 디더링도 제거](https://github.com/pornel/undither#examples)합니다.

**만약 반드시 애니메이션 GIF를 써야겠다면**

*   Gifsicle 같은 툴을 사용하면 메타데이터, 사용하지 않는 색상 팔레트 후보를 제거할 수 있고 프레임 간 변경 사항을 최소화 시킬 수 있습니다.
*   손실 GIF 인코더 사용을 고려해보세요. Gifsicle에서 포크를 떠서 만든 [Giflossy](https://github.com/pornel/giflossy)는 `-lossy` 플래그로 손실 인코딩을 지원하는데, 이를 사용하면 60~65% 정도 파일 크기를 줄일 수 있습니다. Gifsicle을 베이스로 [Gifify](https://github.com/vvo/gifify)라는 나이스한 툴도 있습니다. 비-애니메이션 GIF가 원본이라면 PNG나 WebP로 변환하세요.

더 자세하게 알고 싶다면 Rigor에서 만든 [GIF를 위한 책](https://rigor.com/wp-content/uploads/2017/03/TheBookofGIFPDF.pdf)을 참고하세요.

## <a id="svg-optimization" href="#svg-optimization">SVG 최적화</a>

SVG를 군살 없이 유지한다는 말은 곧 필요 없는 모든 데이터를 제거한다는 것을 뜻합니다. 보통 SVG 파일을 에디터에서 만들면 중복되는 데이터(메타데이터, 주석, 숨겨진 레이어 등등)가 많이 들어가게 됩니다. 렌더링이 될 최종 SVG에는 아무 영향을 주지 않으면서, 필요 없는 데이터를 안전하게 제거하거나 조금 더 간단한 데이터로 변환 할 방법이 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image26.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image26.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image26.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image26.jpg"
        alt="svgo"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image26.jpg"/>
</noscript>
</picture>
<figcaption>Jake Archibald가 만든 [SVGOMG](https://jakearchibald.github.io/svgomg/)는 여러분의 취향에 맞는 방식을 선택하여 SVG 최적화 작업을 할 수 있는 GUI 인터페이스입니다. 실시간으로 최종 마크업 미리보기도 할 수 있습니다.</figcaption>
</figure>

**일반적으로 적용 가능한 SVG 최적화 규칙들:**

* SVG 파일을 최소화(minify)하고 gzip 역시 적용하세요. SVG는 그냥 단지 XML로 표현된 텍스트일 뿐입니다. CSS, HTML, JavaScript와 다를 바가 별로 없습니다. 성능 향상을 하려면 최소화 후 gzip을 적용해야만 합니다.
* 패스를 사용하는 대신에, `<rect>`, `<circle>`, `<ellipse>`, `<line>`, `<polygon>`과 같이 선-정의된 SVG 모양을 사용하세요. 선-정의 모양을 사용하면 최종 이미지 제작을 하는 데 필요한 마크업 양이 줄어들어서 브라우저가 파싱하고 레스터라이즈 해야 하는 코드의 양이 줄어들게 됩니다. SVG 복잡성을 줄인다는 것은 곧 브라우저가 렌더링을 더 빠르게 할 수 있게 됨을 의미합니다.
* 패스를 반드시 써야 하는 상황이라면, 커브와 패스 사용량을 줄여보세요. 할 수 있는 한 간단하게 만들어 조합해 보세요. 일러스트레이터의 [간단화 툴](http://jlwagner.net/talks/these-images/#/2/10)을 사용하면 아트워크가 아무리 복잡하더라도 불필요한 커브 점들을 없애주면서 들쑥날쑥하게 튀어나온 부분들을 매끄럽게 정리해 줍니다.
* 그룹은 되도록 사용하지 마세요. 만약 사용해야 하는 상황이라면, 단순하게 만들어 보세요.
* 보이지 않는 레이어는 삭제하세요.
* 포토샵이나 일러스트레이터 효과 사용은 자제하세요. 용량이 큰 레스터 이미지로 변환될 가능성이 있습니다.
* 끼워 넣은 레스터 이미지가 SVG에 비친화적인 이미지는 아닌지 확인해 두세요.
* SVG 최적화 툴을 사용해 보세요. [SVGOMG](https://jakearchibald.github.io/svgomg/)는 SVGO를 위해 만들어진 정말 편리한 웹 기반 GUI 입니다. Jake Archibald가 만들었는데, 이 툴에 값을 매긴다면 아마 불가능할 겁니다. 만약 스케치를 사용하고 계신다면, [SVGO 압축기 플러그인](https://www.sketchapp.com/extensions/plugins/svgo-compressor/)을 써서 파일을 내보낼 때 용량을 줄여볼 수 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/svgo-precision.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/svgo-precision.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/svgo-precision.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/svgo-precision.jpg"
        alt="SVG 최적화 정확도를 낮추면 때때로 이미지 크기가 줄어드는 효과를 볼 수 있습니다."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/svgo-precision.jpg"/>
</noscript>
</picture>
<figcaption>SVGO를 사용해 SVG 원본 파일을 고-정확 모드(29% 정도 파일 크기 줄어듦)와 저-정확 모드(38% 정도 파일 크기 줄어듦)에서 돌려본 후 차이 비교 예시.</figcaption>
</figure>

[SVGO](https://github.com/svg/svgo)는 SVG 최적화를 할 때 사용하는 노드 기반 툴입니다. SVGO는 여러분이 `<path>`에 정의해 둔 숫자의 정확도를 낮추어 파일 크기를 줄입니다. 소수점 다음에 오는 각각의 숫자들은 1바이트로 치환되어 더해지기 때문에 소수점 정확도(소수점 이하 숫자의 개수)를 바꾸면 파일 크기에 많은 영향을 주게됩니다. 그렇지만 여러분의 SVG 모양에 시각적인 영향을 끼칠 수 있어서 매우 매우 주의해서 정확도를 조절해야 합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image28.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image28.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image28.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image28.jpg"
        alt="최적화가 잘못 되어서 패스와 아트워크가 지나치게 단순화 되었음"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image28.jpg"/>
</noscript>
</picture>
<figcaption>
이 전의 예시에서는 패스나 모양을 과하게 단순화하지 않고 SVGO가 작업을 잘 해 냈지만, 이렇게 되지 않을 수도 있다는 사실에 주의해야 합니다. 정확도가 낮게 설정되었을 때 위의 로켓에 그려진 광선이 어떻게 왜곡되었는지 관찰해보세요.</figcaption>
</figure>

**커맨드라인에서 SVGO 사용하기**

만약 여러분이 GUI보다는 CLI 사용을 선호하신다면, [전역 npm CLI](https://www.npmjs.com/package/svgo)로 SVGO를 설치할 수 있습니다.

```
npm i -g svgo
```

로컬 SVG 파일을 다룰 때는 다음과 같은 명령어를 사용하시면 됩니다.

```
svgo input.svg -o output.svg
```

유동 소수점 정확도 조정을 비롯해 여러분이 필요로 할만한 옵션은 전부 지원합니다.

```
svgo input.svg --precision=1 -o output.svg
```

지원 옵션 목록을 전부 확인해 보려면 SVGO [readme](https://github.com/svg/svgo) 파일을 보세요.

**SVG 압축 하는 것 까먹지 마세요!**

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/before-after-svgo.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/before-after-svgo.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/before-after-svgo.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/before-after-svgo.jpg"
        alt="이미지를 SVG 최적화하기 전과 후."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/before-after-svgo.jpg"/>
</noscript>
</picture>
</figure>

그리고 [SVG 에셋에 Gzip 적용하는 것](https://calendar.perfplanet.com/2014/tips-for-optimising-svg-delivery-for-the-web/) 빼먹지 마세요. 아니면 Brotil로 클라이언트 쪽에 전달하면 됩니다. 텍스트 기반이기 때문에 압축이 매우 잘 됩니다. (원본의 50%까지 압축 가능합니다)

구글에 따르면 로고를 새로 배포했을 때 [가장 작은 크기](https://twitter.com/addyosmani/status/638753485555671040)의 로고는 용량이 고작 305바이트에 지나지 않았다고 합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image30.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image30.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image30.jpg" />

<img
        class="lazyload very-small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image30.jpg"
        alt="구글 로고 중 가장 작은 로고의 크기는 305 바이트 밖에 안된다고 합니다."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image30.jpg"/>
</noscript>
</picture>
</figure>

[고급 SVG 트릭이 아주 많이](https://www.clicktorelease.com/blog/svg-google-logo-in-305-bytes/) 있으므로 크기를 더 줄여보고 싶다면 더 적용해 보시면 됩니다! (146바이트까지 줄여볼 수 있습니다) 툴을 사용하던지 아니면 손수 하던지 간에, 여러분의 SVG 파일 크기를 줄여 볼 여지는 *조금이라도* 더 남아 있다고 충분히 말씀드릴 수 있습니다.

**SVG 스프라이트**

SVG는 아이콘으로 사용하기에 [아주 좋은](https://css-tricks.com/icon-fonts-vs-svg/) 포맷인데, 아이콘 폰트를 사용할 때와는 다르게 [까다로운 꼼수](https://www.filamentgroup.com/lab/bulletproof_icon_fonts.html)를 사용할 필요 없이 스프라이트로 만들어서 시각화할 수 있기 때문입니다. 아이콘 폰트보다 CSS 말을 더 잘 들으면서(SVG 선 속성), 위치 조정도 더 쉽고(가상 요소나 CSS `display` 속성으로 힘들게 해낼 필요가 없습니다) 훨씬 더 [접근성이 우수](http://www.sitepoint.com/tips-accessible-svg/)합니다.

[svg-sprite](https://github.com/jkphl/svg-sprite)나 [IcoMoon](https://icomoon.io/)과 같은 툴을 사용하면 SVG 스프라이트 생성을 자동화할 수 있습니다. [CSS Sprite](https://css-tricks.com/css-sprites/), [Symbol Sprite](https://css-tricks.com/svg-use-with-external-reference-take-2)나 [Stacked Sprite](http://simurai.com/blog/2012/04/02/svg-stacks)에서 SVG 스프라이트를 사용하시면 됩니다. Una Kravetz는 SVG 스프라이트 생성용 gulp-svg-sprite 사용법에 대한 [실용적인 글](https://una.im/svg-icons/#💁)을 썼는데, 읽어볼 만합니다. Sara Soudein 역시 자신의 블로그에 [아이콘 폰트를 SVG로 전환하는 법](https://www.sarasoueidan.com/blog/icon-fonts-to-svg/)에 대한 글을 써서 올려두었습니다.

**더 알아보기**

Sara Soueidan의 '[웹용 SVG 최적화 팁](https://calendar.perfplanet.com/2014/tips-for-optimising-svg-delivery-for-the-web/)'과 Chris Coyier의 '[실용적인 SVG 책](https://abookapart.com/products/practical-svg)'은 정말 훌륭합니다. Andreas Laren의 SVG 최적화 포스팅 역시 계몽적이라고 생각합니다. ([파트 1](https://medium.com/larsenwork-andreas-larsen/optimising-svgs-for-web-use-part-1-67e8f2d4035), [파트 2](https://medium.com/larsenwork-andreas-larsen/optimising-svgs-for-web-use-part-2-6711cc15df46)) '[스케치에서 SVG 아이콘 만들어서 내보내기](https://medium.com/sketch-app-sources/preparing-and-exporting-svg-icons-in-sketch-1a3d65b239bb)'도 읽어보기에 아주 좋은 글입니다.

## <a id="avoid-recompressing-images-lossy-codecs" href="#avoid-recompressing-images-lossy-codecs">손실 코덱으로 이미지를 재압축하지 마세요</a>

압축은 되도록 항상 원본 이미지를 가지고 하세요. 이미 압축된 이미지를 다시 압축한다면 안 좋은 결과를 보게 됩니다. 이미 60 품질로 압축된 JPEG 이미지가 있다고 해봅시다. 이 이미지를 손실 인코딩으로 다시 압축한다면 60일 때보다 보기 안좋게 될 것입니다. 추가로 압축을 한 번 더 할 때마다 데이터 손실이 일어나게 됩니다. 데이터를 잃어버리게 되고 압축으로 인한 아티팩트가 단계마다 증가하게 됩니다. 심지어 고품질로 설정을 한 후 압축을 해도 안 좋기는 마찬가지입니다.

이런 상황을 미연에 방지 하려면, **처음 압축 할 때부터 허용 범위 내의 품질 중 가장 낮은 수치를 선택하세요.** 처음부터 파일 용량을 최대로 줄일 수 있습니다. 여기서 품질을 더 떨어트려서 파일 크기를 줄인다면, 그때부터 즉시 결과물이 좋지 않아 보이기 때문에 계속해서 압축하는 일을 피할 수 있습니다.

손실 압축된 파일을 다시 인코딩하면 거의 항상 파일 용량은 최대로 줄어들게 되나, 그렇다고 한들 여러분이 생각했던 것만큼 품질이 괜찮게 나오지는 않습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/generational-loss.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/generational-loss.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/generational-loss.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/generational-loss.jpg"
        alt="이미지를 여러번 재인코딩 했을 때의 이미지 손상"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/generational-loss.jpg"/>
</noscript>
</picture>
<figcaption>상단 이미지는 Jon Snevers가 만든 [훌륭한 영상](https://www.youtube.com/watch?v=w7vXJbLhTyI)과 [관련 글](http://cloudinary.com/blog/why_jpeg_is_like_a_photocopier)에서 따온 것인데, 압축을 계속 할수록 이미지에 발생하는 품질 손실을 포맷별로 보여주고 있습니다. 여러분이 만약 SNS에서 (이미 압축이 된) 이미지를 내려받은 후 이를 다시 웹상에 올린다면 (이때 압축이 다시 됩니다) 발생할 문제점들을 보여줍니다. 이미지 품질이 점점 악화됩니다.</figcaption>
</figure>

trellis 양자화(quantization) 덕분에 MozJPEG을 사용하면 압축을 다시 해도 품질 손상 영향을 덜 주게 됩니다. (아마 우연적인 결과 같습니다) 이 양자화 기법에서는 모든 DCT 값을 정확하게 압축하는 대신, +1/-1 범위 내의 값 중에서 단 몇 비트라도 더 압축할 수 있는 값을 찾아냅니다. 손실 FLIF는 (재)압축 전에는 손실 PNG와 매우 비슷합니다. 이미지 데이터를 살펴본 후, 어떤 것을 버릴지 결정 내립니다. 재압축된 PNG는 데이터가 더 변경되는 것을 방지하기 위해 이를 감지하는 ‘구멍’들을 가지고 있습니다.

**원본 파일을 편집할 때는, PNG와 TIFF같은 무손실 포맷으로 저장해서 할 수 있는 한 최상의 품질로 보관하세요.** 그러면 여러분이 사용하는 빌드 툴이나 이미지 압축 서비스를 거쳐 나온 결과물을 사용자들에게 보여주더라도 품질 손실을 최소화 할 수 있습니다.

## <a id="reduce-unnecessary-image-decode-costs" href="#reduce-unnecessary-image-decode-costs">불필요한 디코딩 및 크기변환 하지 않기</a>

지금까지는 사용자들이 필요한 것 이상으로 크기가 크고 해상도 높은 이미지를 제공하였습니다. 이로 인해 치러야 할 비용이 있습니다. 이미지 디코딩과 크기 변환은 보통 모바일 하드웨어 상에서 브라우저가 수행하는 작업 중에 비용이 높은 작업에 속합니다. 큰 이미지를 서버에서 내려보내서 CSS나 width/height 속성으로 크기를 변환하면, 다음 그림과 같은 일이 벌어지는 것을 보게 될 것이고, 이로 인해 성능에 영향을 주게 됩니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1503503389/essential-image-optimization/image-pipeline.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1503503389/essential-image-optimization/image-pipeline.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503503389/essential-image-optimization/image-pipeline.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503503389/essential-image-optimization/image-pipeline.jpg"
        alt="브라우저가 이미지 태그 내에 명시된 이미지를 가져오고 화면에 표시하기까지는 많은 과정을 거쳐야 합니다. 요청해서 받아온 이미지를 디코딩하고, 크기를 조정해서 GPU로 복사한 후에 화면에 표시하게 됩니다."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503503389/essential-image-optimization/image-pipeline.jpg"/>
</noscript>
</picture>
<figcaption>
브라우저가 이미지를 가져오면, 원본 파일 형식(예: JPEG)을 메모리상의 비트맵으로 디코딩해야 합니다. 이미지 크기를 변환(예: 컨테이너 크기에 맞게 너빗값을 퍼센트로 맞추기)하는 작업도 자주 하게 됩니다. 이미지 디코딩 및 크기 변환 작업은 비용 작업이 높을뿐더러 이미지가 화면에 표시될 때까지 걸리는 시간도 늘어납니다.</figcaption>
</figure>

이상적인 상황은 크기 변환을 전혀 할 필요 없이 브라우저에 렌더링할 수 있는 이미지를 보내는 경우입니다. 그러므로 여러분이 목표로 하는 스크린 크기와 해상도에 맞는 가장 작은 이미지를 서버에서 전송하도록 하세요. [`srcset`과 `sizes`](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)의 장점을 이용해서 말입니다. `srcset`에 대해서는 잠시 후에 다루도록 하겠습니다.

또한, 이미지의 `width`와 `height` 속성을 없애버리면 성능이 안 좋아질 수 있습니다. 이 속성이 없다면 브라우저는 이미지가 들어갈 곳에 실제 이미지 크기보다 작은 플레이스홀더 영역을 일단 넣은 후, 이미지 크기가 가늠될 만큼 바이트가 충분히 들어오기 전까지 플레이스홀더 영역을 유지합니다. 플레이스홀더를 이미지로 교체하려면 도큐먼트 레이아웃이 반드시 업데이트되어야 하는데, 브라우저 렌더링 과정 중에 수행 비용이 많이 드는 '리플로우' 단계에서 이 작업이 수행됩니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/devtools-decode.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/devtools-decode.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/devtools-decode.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/devtools-decode.jpg"
        alt="크롬 개발자 도구에 나오는 이미지 디코딩 비용"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/devtools-decode.jpg"/>
</noscript>
</picture>
<figcaption> 화면 위에 이미지를 띄우려면 브라우저가 밟아야 할 스텝이 몇 가지 있습니다. 서버로부터 이미지를 받아와야 할 뿐만 아니라, 디코딩도 해야 하고 크기도 다시 조정해야 합니다. 크롬 개발자 도구의 [Timeline](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/performance-reference) 패널을 사용해 여기서 일어나는 이벤트를 조사해 볼 수 있습니다.</figcaption>
</figure>

이미지 크기가 커질수록 메모리 크기 비용 또한 증가하게 됩니다. 디코딩된 이미지는 개별 픽셀의 크기가 4바이트 이내입니다. 조심하지 않는다면 브라우저에서 충돌이 발생할 수도 있습니다. 저가 기기에서는 메모리 스와핑이 그리 오래 지나지 않아 발생합니다. 그러므로 이미지 디코딩과 크기 변환, 메모리 비용에 대해 신경을 써주세요.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1503695136/essential-image-optimization/image-decoding-mobile.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1503695136/essential-image-optimization/image-decoding-mobile.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503695136/essential-image-optimization/image-decoding-mobile.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503695136/essential-image-optimization/image-decoding-mobile.jpg"
        alt="일반적인 저가 모바일 폰에서 이미지 디코딩에 들어가는 비용은 놀랄만큼 높습니다. 어떨 때는 디코딩할 때 5배 정도 느려지기도 합니다."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503695136/essential-image-optimization/image-decoding-mobile.jpg"/>
</noscript>
</picture>
<figcaption>일반적인 저가 모바일 폰에서 이미지 디코딩에 들어가는 비용은 놀랄 만큼 높습니다. 어떨 때는 디코딩할 때 5배 정도 느려지기도 합니다. (이것보다 더 느려지지 않는다면요)</figcaption>
</figure>

트위터에서 [모바일 웹 UX](https://medium.com/@paularmstrong/twitter-lite-and-high-performance-react-progressive-web-apps-at-scale-d28a00e780a3) 개편을 진행하면서, 사용자에게 꼭 필요한 크기의 이미지만을 확실하게 제공하는 기능을 넣어 이미지 디코딩 성능을 향상했습니다. 트위터 타임라인에 노출되는 수많은 이미지를 디코딩하는데 걸리는 최대 속도가 400ms에서 19ms로 단축되었습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/image-decoding.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/image-decoding.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/image-decoding.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/image-decoding.jpg"
        alt="트위터 라이트 서비스에서 이미지 파이프라인 최적화 전(붉은색)과 후(초록색)의 이미지 디코딩 시간을 크롬 개발자 도구 타임라인/성능 패널에 표시해 보았습니다. 최적화 전에 시간이 더 오래 걸렸습니다."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/image-decoding.jpg"/>
</noscript>
</picture>
<figcaption>
트위터 라이트 서비스에서 이미지 파이프라인 최적화 전(붉은색)과 후(초록색)의 이미지 디코딩 시간을 크롬 개발자 도구 타임라인/성능 패널에 표시해 보았습니다.</figcaption>
</figure>

### <a id="delivering-hidpi-with-srcset" href="#delivering-hidpi-with-srcset">`srcset` 사용해서 HiDPI 이미지 전달하기</a>

사용자가 고해상도 화면이 내장된 다양한 모바일 및 데스크톱 기기로 여러분의 사이트에 접속할 수도 있습니다. [기기 픽셀 비율](https://stackoverflow.com/a/21413366)(Device Pixel Ratio, DPR, "CSS 픽셀 비율"이라고도 불립니다)은 기기의 화면 해상도가 CSS에서 어떻게 변환이 되는지를 나타내는 수치입니다. DPR은 폰 제조사들이 만든 수치로, 이를 활용하면 화면상의 요소가 너무 작게 나오는 일이 없게 하면서 모바일 스크린의 해상도와 선명도를 높일 수 있습니다.

사용자가 기대하는 만큼의 이미지 품질을 맞추기 위해서는 해상도를 가장 적합하게 조절하여 이미지를 사용자 기기로 전달해야 합니다. 선명하면서 DPR이 높은 이미지(예를 들어 2배, 3배 이미지)를 지원하는 기기라면 이 이미지들을 보내면 됩니다. 2배 이상의 이미지는 대부분 용량이 훨씬 더 많이 나가기 때문에, 고해상도 화면으로 보지 않는 사용자에게는 선명도가 낮고 DPR 수치가 표준인 이미지를 전달해야 합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502570356/essential-image-optimization/device-pixel-ratio.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502570356/essential-image-optimization/device-pixel-ratio.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502570356/essential-image-optimization/device-pixel-ratio.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502570356/essential-image-optimization/device-pixel-ratio.jpg"
        alt="1배, 2배, 3배에서 디바이스 픽셀 비율 도표. DPR 수치가 높을수록 이미지 선명도가 높아짐을 확인할 수 있고, 디바이스 픽셀과 CSS 픽셀을 비교한 이미지가 있다."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502570356/essential-image-optimization/device-pixel-ratio.jpg"/>
</noscript>
</picture>
<figcaption>기기 픽셀 비율: [material.io](https://material.io/devices/)와 [mydevice.io](https://mydevice.io/devices/)를 비롯한 많은 사이트에서 자주 사용되는 기기들의 DPR을 추적하고 있습니다.</figcaption>
</figure>

[srcset](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Responsive_images)을 사용하면 브라우저가 기기에 맞춰서 가장 적합한 이미지를 선택하도록 만듭니다. 예를 들어, 2배 모바일 디스플레이에서는 2배 이미지를 선택하는 식입니다. `srcset` 지원을 하지 않는 브라우저도 대응하려면 `<img>` 태그에 기본 `src` 속성을 명시해 두면 됩니다.

```
<img srcset="paul-irish-320w.jpg,
             paul-irish-640w.jpg 2x,
             paul-irish-960w.jpg 3x"
     src="paul-irish-960w.jpg" alt="Paul Irish cameo">
```

[Cloudinary](http://cloudinary.com/blog/how_to_automatically_adapt_website_images_to_retina_and_hidpi_devices)와 [Imgix](https://docs.imgix.com/apis/url/dpr) 같은 Image CDN에서는 아예 단일 표준 이미지를 사용자에게 제공하거나, 아니면 사용자마다 가장 적합한 배율의 이미지를 선택하여 제공할 수 있습니다.

<aside class="note"><b>알아두기:</b> [Udacity](https://www.udacity.com/course/responsive-images--ud882) 무료 코스나 Web Fundamentals의 [이미지 가이드](https://developers.google.com/web/fundamentals/design-and-ui/responsive/images)를 통해 DPR과 반응형 이미지에 대해 좀 더 알아볼 수 있습니다.</aside>

친절하게 하나 더 알려드리자면, 사용 가능한 픽셀 밀도와 포맷 조합을 전부 반응형 이미지 마크업에 나열하는 대신에 ['클라이언트 힌트'](https://www.smashingmagazine.com/2016/01/leaner-responsive-images-client-hints/)를 사용해 볼 수도 있습니다. 클라이언트 힌트를 사용하면 관련 정보를 HTTP 요청에 붙이므로 이를 통해 웹 서버가 현재 기기의 화면 비율에 가장 알맞은 이미지를 선택해 내보내줄 수 있습니다.

### <a id="art-direction" href="#art-direction">아트 디렉션</a>

사용자에게 최선의 화질로 이미지를 전달하는 것도 중요하지만, 때에 따라서는 사이트를 **[아트 디렉션](http://usecases.responsiveimages.org/#art-direction)의 관점에서(디자인적인 관점에서)** 생각해 볼 필요도 있습니다. 만약 사용자 화면 크기가 작아진다면, 가용 공간을 최대한 활용하여 이미지를 자르거나 확대하여 보여주고 싶을 수도 있습니다. 디자인적인 이야기는 이 글의 관점에서 조금 벗어나기는 하지만, [Cloudinary](http://cloudinary.com/blog/automatically_art_directed_responsive_images%20)와 같은 서비스에서는 이런 이미지 조작을 최대한 자동화하여 처리할 수 있는 API를 제공하고 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/responsive-art-direction.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/responsive-art-direction.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/responsive-art-direction.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/responsive-art-direction.jpg"
        alt="실제로 구현해 본 반응형 아트 디렉션. 기기에 따라 이미지를 잘라서 덜 보여주거나 더 보여주고 있다."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/responsive-art-direction.jpg"/>
</noscript>
</picture>
<figcaption>Eric Portis는 훌륭한 [예시](https://ericportis.com/etc/cloudinary/)를 통해 아트 디렉션 측면에서 보았을 때 반응형 이미지가 어떻게 나타나야 하는지 보여줍니다. 이 예시에서는 가용 공간을 최대한 잘 활용하기 위해 각각의 중단점마다 메인 히어로 이미지가 다르게 보이도록 조정됩니다.</figcaption>
</figure>

## <a id="color-management" href="#color-management">색상 관리</a>

색을 볼 때는 적어도 생물학, 물리학, 프린트 이 세가지 관점에서 관찰해야 합니다. 생물학적으로 색을 정의해 본다면 [지각 현상](http://hubel.med.harvard.edu/book/ch8.pdf)이라 합니다. 사물에 반사되는 빛은 다양한 형태의 파장으로 이루어져 있습니다. 우리 눈의 광수용체는 사물로부터 반사되어 나오는 파장을 받아들여 우리가 색으로 알고 있는 감각으로 인식하게 합니다. 물리학에서 빛은 중요한 위치를 차지합니다. 정확히 말하자면 광주파수와 밝기입니다. 프린트 쪽을 살펴보면 색상에 있어서 중요한 것은 색상환(color wheel)과 잉크, 그리고 예술의 대상이 되는 모델입니다.

이상적인 세상에서는 모든 화면과 웹 브라우저에서 색이 동일하게 표현될 것입니다. 그러나 안타깝게도 태생적인 한계 때문에 그렇지 못합니다. 색상 관리 기능을 사용하면 색 모델과 공간, 프로파일에서 표현하는 색 간의 화합을 이루어낼 수 있을 겁니다.

#### 색 모델

[색 모델](https://en.wikipedia.org/wiki/Gamma_correction)은 색 범위를 정의하는 시스템으로, 원색(primary color) 몇 가지만 대상으로 정의할 수도 있고 모든 범위의 색을 다 다룰 수도 있습니다. 일부 색 공간은 다른 공간들보다 조정할 수 있는 변수가 적습니다. 예를 들어 그레이스케일 공간은 흑백 색상 사이의 밝기를 조절할 수 있는 변수 하나만 가지고 조정해야 합니다.

널리 사용되는 두 가지 색 모델은 가산(additive) 모델과 감산(subtractive) 모델입니다. 가산 색 모델(예: 디지털 화면에 사용되는 RGB)은 빛을 사용해 색을 표현하는 반면, 가산 색 모델은(예: 인쇄에 사용되는 CMYK)는 기존 색에서 빛을 뺌으로써 색을 표현합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504564914/colors_ept6f2.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1504564914/colors_ept6f2.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504564914/colors_ept6f2.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504564914/colors_ept6f2.jpg"
        alt="sRGB, Adobe RGB, ProPhoto RGB" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504564914/colors_ept6f2.jpg"/>
</noscript>
</picture>
<figcaption>RGB에서는 빨간색, 초록색, 파란색 빛을 조합하여 색을 넓은 범위로 표현합니다. CYMK(Cyan, Magenta, Yellow and Black)은 서로 다른 잉크를 사용해 흰색 종이에서 빛을 빼내어 색을 표현합니다.</figcaption>
</figure>

[색 모델과 스폿 컬러 시스템 이해하기](https://www.designersinsights.com/designer-resources/understanding-color-models/) 글에는 HSL, HSV, LAB과 같은 기타 색 모델과 모드에 대한 설명이 잘 되어 있습니다.

#### 색 공간

[색 공간](http://www.dpbestflow.org/color/color-space-and-color-profiles#space)은 주어진 이미지가 있을 때, 이 이미지 안에 담긴 특정 범위 안의 색으로 나타낼 수 있습니다. 예를 들어 만약 이미지에 1670만 개의 색상이 담겨 있는데, 색 공간이 달라지게 된다면 표현 가능한 색의 범위가 좁아지거나 넓어지게 됩니다. 일부 개발자들은 색 모델과 색 공간을 같은 것으로 간주합니다.

[sRGB](https://en.wikipedia.org/wiki/SRGB)는 웹 [표준](https://www.w3.org/Graphics/Color/sRGB.html) 색 공간으로 고안된 것이며, RGB를 기반으로 합니다. 크기가 작은 색 공간으로 최소한의 공통 색 분모가 들어있어 크로스 브라우징을 고려한 색 관리를 하고자 할 때 안전한 선택지가 됩니다. 이 외 다른 색 공간은 ([Adobe RGB](https://en.wikipedia.org/wiki/Adobe_RGB_color_space)나 [ProPhoto RGB](https://en.wikipedia.org/wiki/ProPhoto_RGB_color_space) – Photoshop과 Lightroom에서 사용됨) sRGB보다 더 다양한 색을 표현할 수 있으나, sRGB가 대부분의 웹 브라우저와 게임, 모니터에서 호환이 되는 공용성을 갖추었으므로 sRGB의 비중이 대개 가장 큽니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504565044/color-wheel_hazsbk.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1504565044/color-wheel_hazsbk.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504565044/color-wheel_hazsbk.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504565044/color-wheel_hazsbk.jpg"
        alt="sRGB, Adobe RGB and ProPhoto RGB" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504565044/color-wheel_hazsbk.jpg"/>
</noscript>
</picture>
<figcaption>위의 그림은 색 영역(gamut, 색 공간이 정의할 수 있는 색상 범위)을 시각화한 것입니다.</figcaption>
</figure>

색 공간에는 세 개의 채널이 있습니다. (빨강, 초록, 파랑) 각 채널에서는 8bit 모드에서 255가지 색상을 표현할 수 있으므로, 약 1670만 개의 색상이 표현 가능합니다. 16bit 이미지라면 수조 개의 색상이 표현 가능합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504564915/srgb-rgb_ntuhi4.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1504564915/srgb-rgb_ntuhi4.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504564915/srgb-rgb_ntuhi4.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504564915/srgb-rgb_ntuhi4.jpg"
        alt="sRGB, Adobe RGB와 ProPhoto RGB" />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504564915/srgb-rgb_ntuhi4.jpg"/>
</noscript>
</picture>
<figcaption>[Yardstick](https://yardstick.pictures/tags/img%3Adci-p3)에서 가져온 이미지를 바탕으로 sRGB, Adobe RGB, ProPhoto RGB 비교. sRGB로는 우리 눈으로 볼 수 없는 색을 표현하기가 엄청나게 어렵습니다. sRGB로 된 일반 사진과 넓은 색 영역으로 표현된 사진을 비교해보자면 하나만 빼고 모든 것이 동일한데, 그것은 바로 채도가 극단적으로 높게 표현되는 '먹음직스러운' 색입니다.</figcaption>
</figure>

색 공간 사이의 다른점(sRGB, Adobe RGB, ProPhoto RGB처럼)은 각각의 색 영역(그림자까지 추가하여 만들어낼 수 있는 색 범위), 발광도(illuminant)와 [감마](http://blog.johnnovak.net/2016/09/21/what-every-coder-should-know-about-gamma/) 곡선입니다. sRGB는 Adobe RGB보다 20%정도 영역이 작고, ProPhoto RGB는 Adobe RG 이보다 50%가량 넓습니다. 위 이미지는 [Clipping Path](http://clippingpathzone.com/blog/essential-photoshop-color-settings-for-photographers)에서 가져왔습니다.

[넓은 색영역(Wide-gamut)](http://www.astramael.com/)은 sRGB보다 넓은 색 영역을 지닌 공간을 일컬을 때 사용하는 단어입니다. 광색영역을 지닌 모니터가 점점 많아지는 추세입니다. 다르게 말하자면 아직도 많은 디지털 디스플레이에서는 sRGB보다 훨씬 더 나은 컬러 프로필을 화면에 표시할 수 없는 상태라는 뜻입니다. 타겟 유저가 비싼 광색영역 화면을 사용하지 않는 이상, 포토샵에서 '웹용으로 저장'할 때, 'sRGB로 변환(Convert to sRGB)' 옵션 선택을 한번 고려해 보세요.

<aside class="key-point"><b>알아두기:</b> 원본 사진을 편집할 때 sRGB를 주요 색공간으로 사용하는 것은 피하세요. sRGB는 대부분의 카메라가 지원할 수 있는 색공간보다 영역이 좁고 클리핑 현상이 일어날 수 있습니다. 대신에 (ProPhoto RGB 같은) 더 넓은 색공간에서 작업을 하고 웹용으로 저장할 때 sRGB로 옮기세요.</aside>

**웹 콘텐츠 용으로 광색영역을 쓸 만한 경우가 있나요?**

네. 만약 이미지 색상이 매우 쨍하고/실감나 보이고/역동적이면서, 광색영역을 지원하는 화면에서도 색이 원본처럼 나타나도록 신경써야 한다면 광색영역 사용이 적합한 상황입니다. 그러나 실제 사진에서는 그런 경우가 거의 없습니다. sRGB 광색영역 범위를 실제로 넘지 않고서도 색을 손쉽게 조작하여 역동적이게 보이도록 할 수 있습니다.

사람 눈으로 인식되는 색은 절대적인 색상이 아니며, 주위 환경에 상대적이라 쉽게 속는 편입니다. 만약 이미지에 형광 강조색이 들어가 있다면 이런 색은 광색영역 화면에서 더 눈에 잘 띄게 됩니다.

#### 감마 보정과 압축

[감마 보정](https://en.wikipedia.org/wiki/Gamma_correction)(혹은 그냥 감마)을 하면 이미지의 전체적인 밝기를 조정할 수 있습니다. 또한 감마 보정으로 녹색이나 파란색 대비 빨간색의 비율을 조정할 수 있습니다. 감마 보정을 하지 않은 이미지는 탈색되어 보이거나 색상이 너무 진하게 보일 수 있습니다.

비디오나 컴퓨터 그래픽 분야에서는 데이터 압축과 유사하게 감마가 압축용으로 사용됩니다. 감마 덕분에 유용한 밝기 레벨 단계를 어느정도 모아 몇비트(12나 16보다는 8bit)정도로 압축할 수 있습니다. 밝기에 대한 사람의 인식은 물리적인 빛의 양에 선형적으로 비례하지 않습니다. 우리 눈에 적합한 이미지로 인코딩하려는 데 색상을 순수 물리적인 형태로 나타내서 사용한다면 그다지 쓸모없는 일을 한 셈입니다. 감마 압축은 밝기의 규모를 사람의 인식 범위에 가깝게 인코딩하는 용도로 씁니다.

후에 유용하게 사용할 수 있는 밝기 규모를 감마 압축한다면 정확도를 8bit(대부분의 RGB 색상에서 쓰는 0-255)안으로 맞출 수 있습니다. 색상이 특정 물리적 단위와 1:1 관계를 가진다고 하면, RGB 값은 1에서 백만 사이의 단위로 표현이 가능할텐데, 여기서 값이 0~1000 사이면 차이가 두드러지게 보이고, 999000~1000000 사이의 값이면 거의 동일하게 보이는 현상이 발생합니다. 이 현상에서 착안된 것이 감마 압축입니다. 여러분이 촛불이 달랑 하나 밝혀진 어두운 방 안에 있다고 상상해 보세요. 두번째 촛불에 불을 밝히면 방 안의 불빛양이 증가한 것을 대번 알아차릴 수 있습니다. 심지어 여기서 세번째 촛불에 불을 붙이면 이보다 더 밝아지기까지 합니다. 자, 이제 촛불 100개가 밝혀진 방 안에 있다 해봅시다. 101번째 촛불에 불을 밝히고, 102번째를 그 다음에 켜봅시다. 아마 밝기 변화를 알아차리지 못할 겁니다.

두번째 촛불을 밝히나, 101번째 촛불을 밝히나 물리적으로는 정확히 동일한 양의 빛이 추가되었습니다. 따라서 우리 눈은 빛이 밝을 때가 어두울 때보다 밝기 변화에 덜 민감하다는 사실을 도출할 수 있으므로, 감마 압축은 밝기 값을 '압축'하여 물리학적 용어로 '밝기 레벨'의 정확도를 떨어트리지만 척도를 사람 눈에 적합하게 조정하여 눈으로 보기에 압축 전의 값과 달라진 점이 없이 똑같아 보이게 됩니다.

<aside class="key-point"><b>알아두기:</b> 이 글에서 설명한 감마 압축/보정은 포토샵에서 볼 수 있는 이미지 감마 곡선과는 다릅니다. 감마 압축이 제대로 되었다면 아무일도 일어나지 않은 것처럼 보이니까요.</aside>

#### 색 프로파일

색 프로파일은 디바이스의 색 공간에 대한 정보입니다. 색 공간을 변환할 때 사용됩니다. 프로파일을 사용하면 각기 다른 화면과 매체에서 이미지가 최대한 동일하게 나타나도록 조정할 수 있습니다.

이미지에는 [국제 컬러 협회(International Color Consortium, ICC)](http://www.color.org/icc_specs2.xalter)가 제정한 컬러 프로파일이 내장되어 있어서, 이를 들여다보면 색상을 정확히 어떻게 표현해야 하는지 알 수 있습니다. JPEG, PNG, SVG, [WebP](https://developers.google.com/speed/webp/docs/riff_container)등과 같은 여러 포맷과 대부분의 주요 브라우저에서 내장 ICC 프로파일을 가져다 사용할 수 있습니다. 앱 화면에 이미지가 나타난 후 앱이 모니터 성능에 대해 알고 있다면 색 프로파일을 이용해 색상을 조절합니다.

<aside class="key-point"><b>알아두기:</b> 일부 모니터에는 sRGB와 비슷한 색 프로파일이 들어 있고, 때로는 이보다 더 나은 프로파일이 제공될 경우 이를 화면에 제대로 표현 못할 가능성이 있습니다. 그러므로 여러분의 사용자층에 따라 색 프로파일에 넣을 수 있는 값에 한계가 있을 수 있어서, 사용자층 파악을 할 필요가 있습니다.</aside>

이미지에 색 프로파일을 넣으면 파일 크기가 크게 증가할 수 있으므로, 넣으실 때 주의하시기 바랍니다. (때로는 100KB 이상 증가합니다) ImageOptim과 같은 툴을 사용하면 색 프로파일을 찾는 즉시 [자동으로](https://imageoptim.com/color-profiles.html) 지워버릴 수도 있습니다. 이와는 다르게 파일 크기 감소라는 명목하에 ICC 프로파일을 지울 경우 브라우저가 모니터 색공간에 맞추어 이미지를 보여주게 될 수도 있어서 기대했던 것 과는 다르게 채도와 대비가 표현될 수도 있습니다. 이런 장단점을 참고하여 여러분의 상황에 맞는 선택을 해주세요.

[Nine Degrees Below](https://ninedegreesbelow.com/photography/articles.html) 사이트에는 ICC 프로파일 색상 관리에 관한 훌륭한 자료들이 있으니 프로파일에 대해 더 알아보고 싶다면 참고하시면 됩니다.

#### 색 프로파일과 웹 브라우저

크롬 초기 버전에서는 색 관리에 대한 지원이 그다지 좋은 편은 아니었으나, 2017년에 [색 보정 렌더링(Color Correct Rendering)](https://groups.google.com/a/chromium.org/forum/#!topic/blink-dev/ptuKdRQwPAo) 기능이 개선되었습니다. sRGB가 아닌 화면(신규 맥북 프로)은 sRGB 색공간을 디스플레이 프로파일에 맞추어 변환시킵니다. 따라서 여러 시스템과 다양한 브라우저를 통해 이미지를 보면 모두 색상이 비슷하게 보일 것입니다. 사파리, 엣지, 파이어폭스 브라우저도 ICC 프로파일을 사용할 수 있으므로, 색 프로파일이 다른 이미지가 있다 해도 브라우저가 알아서 색공간을 조정하므로 화면의 넓은 범위 색영역 지원 여부와는 상관없이 동일하게 색이 표현됩니다.

<aside class="key-point"><b>알아두기:</b> 현재 통용되는 여러 웹 색상 표현 방법에 대해 더 알아보고 싶다면, Sarah Drasner가 쓴 [nerd’s guide to color on the web](https://css-tricks.com/nerds-guide-color-web/)를 참고하세요.</aside>

## <a id="image-sprites" href="#image-sprites">이미지 스프라이트 기법</a>

웹이 만들어진 이후 오랫동안 사용되어 온 [이미지 스프라이트](https://developers.google.com/web/fundamentals/design-and-ui/responsive/images#use_image_sprites) 기법(때로는 CSS 스프라이트라고도 불립니다)은 모든 브라우저에서 사용할 수 있습니다. 잘라놓은 이미지들을 하나의 큰 판으로 합쳐서 페이지 로딩 시 이미지 요청 수를 줄이는 용도로 널리 사용되고 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1503693437/essential-image-optimization/i2_2ec824b0_1.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1503693437/essential-image-optimization/i2_2ec824b0_1.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503693437/essential-image-optimization/i2_2ec824b0_1.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503693437/essential-image-optimization/i2_2ec824b0_1.jpg"
        alt="구글 홈페이지를 비롯해 실제 운영 중인 대규모 사이트에서 이미지 스프라이트 기법은 여전히 많이 사용되고 있습니다."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503693437/essential-image-optimization/i2_2ec824b0_1.jpg"/>
</noscript>
</picture>
<figcaption>구글 홈페이지를 비롯해 실제 운영 중인 대규모 사이트에서 이미지 스프라이트 기법은 여전히 많이 사용되고 있습니다.</figcaption>
</figure>

HTTP/1.x에서는 HTTP 요청수를 줄이려고 스프라이트를 사용합니다. 이렇게 하면 장점도 있으나, 캐시 무효화(cache-invalidation) 문제가 쉽사리 발생할 위험이 있으므로 조심해야 합니다. (스프라이트 이미지가 조금만 바뀌어도 캐시에 있던 스프라이트 이미지 전체가 무효가 됩니다)

어쩌면 이제 스프라이트 기법은 [HTTP/2](https://hpbn.co/http2/)의 안티 패턴이 될 수도 있을 것 같습니다. HTTP/2에서는 연결을 한 번만 해도 요청을 여러 번 보낼 수 있어서, [이미지를 개별적으로 불러들이는 편](https://deliciousbrains.com/performance-best-practices-http2/)이 제일 나은 방법일지도 모르겠습니다. 여러분이 설정한 네트워크 환경에서 이미지를 따로따로 불러들이는 편이 더 좋은지 아닌지를 판단하려면 성능 측정을 해 보세요.

## <a id="lazy-load-non-critical-images" href="#lazy-load-non-critical-images">중요하지 않은 이미지는 레이지 로딩</a>

레이지 로딩 기법은 특정한 이미지가 있을 때, 사용자가 이미지를 볼 필요성을 느끼기 전까지 브라우저에서 로딩 시점을 늦추는 웹 성능 패턴입니다. 사용 예시로는 스크롤을 할 때 이미지가 필요할 때마다 비동기적으로 로딩되는 경우를 들어볼 수 있습니다. 레이지 로딩을 통해 이미지 압축 전략으로 이미 줄여 놓은 데이터 사용량을 조금 더 줄여볼 수 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/scrolling-viewport.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/scrolling-viewport.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/scrolling-viewport.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/scrolling-viewport.jpg"
        alt="레이지 로딩 중인 이미지들"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/scrolling-viewport.jpg"/>
</noscript>
</picture>
</figure>

스크롤 내리기 전의 최상단 화면(above the fold)에 위치해야 하는 이미지, 맨 처음 나타나는 웹 페이지는 곧바로 로딩됩니다. 하지만 "스크롤 내린 후" 뒤따라 나타나는 이미지들은 아직 사용자가 보기 전입니다. 이런 이미지들은 브라우저가 곧바로 불러올 필요가 없습니다. 나중에 불러와도 됩니다. 아니면 느릿느릿 불러오거나요. 사용자가 스크롤을 아래로 내려서 보여줄 필요가 있을 때만 불러오는 것입니다.

아직은 브라우저에 레이지 로딩 기능이 기본으로 내장되어 있지는 않습니다. (예전에 이에 대한 [논의](https://discourse.wicg.io/t/a-standard-way-to-lazy-load-images/1153/10)가 있었긴 합니다) 대신에 자바스크립트를 사용하여 이 기능을 추가할 수 있습니다.

**왜 레이지 로딩 기법이 유용할까요?**

꼭 필요할 때에만 ‘느릿하게’ 이미지를 불러오면 장점이 많이 생기게 됩니다.

* **데이터 소모량 감소**: 사용자가 모든 이미지를 미리 가지고 있을 필요가 없다고 가정해 놓은 상태라면 최소한의 리소스만 불러오면 됩니다. 이는 항상 좋은 일이며, 특히 데이터 사용량 제한 플랜을 사용하는 모바일 기기일수록 이득을 더 보게 됩니다.
* **배터리 소모량 감소**: 사용자 기기의 브라우저에서 불러 들어야 하는 양이 줄어들므로 기기의 배터리 수명을 아낄 수 있습니다.
* **다운로드 속도 향상**: 이미지가 많은 웹사이트의 페이지 로딩 시간이 전에는 몇초 걸렸다면, 이를 거의 0초로 만드는 경우 사용자 경험을 엄청나게 향상할 수 있습니다. 사이트 반송률(bounce statistic)을 여러분의 사이트에 체류하며 둘러보면서 즐기는 사용자로 전환할 수 있습니다.

**그러나 다른 모든 툴처럼, 큰 힘에는 큰 책임이 따라오기 마련입니다.**

**화면 최상단에 오는 이미지는 레이지 로딩하지 마십시오.** 이 기법은 이미지 목록이 길거나 (예를 들어 상품 리스트) 사용자 아바타 목록과 같은 곳에 적용하세요. 메인 페이지에 오는 주요 히어로 이미지에는 사용하지 마십시오. 최상단 이미지에 이 기법을 적용하면 사람의 눈으로 볼 때나 기술적으로 볼 때나 로딩 속도가 모두 느리다고 인식될 가능성이 있습니다. 브라우저의 프리로더(preloader), 프로그레시브 로딩 과정을 중단시킬 수도 있으며 자바스크립트가 브라우저에 일을 추가로 더 시키는 결과를 초래할 수도 있습니다.

**스크롤링과 동시에 이미지를 레이지 로딩하는 경우에는 매우 주의를 기울여야 합니다.** 만약 사용자가 스크롤 하는 것을 기다린다면, 먼저 플레이스홀더 이미지가 화면에 나오게 될 것이고, 이 밑으로 더 스크롤링하지 않는다면 결국엔 이미지를 보게 됩니다. 제가 추천해 드리는 한 가지 방법은, 최상단 이미지가 로딩된 후에 나머지 이미지들을 레이지 로딩하기 시작해서 사용자 인터렉션과는 별개로 모든 이미지를 불러오는 것입니다.

**누가 이 기법을 사용 중인가요?**

레이지 로딩 적용 예를 보시려면, 이미지를 대량으로 호스팅하는 주요 사이트 중 거의 아무 데나 들어가 보시면 됩니다. 유명한 사이트를 예시로 들자면 [Medium](https://medium.com/)과 [Pinterest](https://www.pinterest.com/)가 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image35.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image35.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image35.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image35.jpg"
        alt="medium.com에서 이미지 인라인 미리보기"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image35.jpg"/>
</noscript>
</picture>
<figcaption>Medium.com에서는 이미지 미리보기를 가우시안 블러 처리하여 화면에 보여줍니다.</figcaption>
</figure>

Medium과 같은 몇몇 사이트에서는 가우시안 블러 처리된 미리보기 이미지(용량은 수백 바이트 정도)를 작은 크기로 보여주는데, 이 미리보기용 이미지는 원본 이미지가 다 받아지면 해당 원본 이미지로 전환(레이지 로딩)이 됩니다.

José M. Pérez는 [CSS 필터](https://jmperezperez.com/medium-image-progressive-loading-placeholder/)를 써서 Medium에서 사용 중인 효과를 어떻게 구현하면 되는지, 그리고 [각기 다른 이미지 포맷](https://jmperezperez.com/webp-placeholder-images/)을 사용해 이러한 플레이스홀더용 이미지를 구현해 본 실험에 대한 글을 썼습니다. 페이스북에서도 [커버 사진](https://code.facebook.com/posts/991252547593574/the-technology-behind-preview-photos/)의 플레이스홀더 영역 용량을 어떻게 그 유명한 '200바이트'로 만들었는지 그 접근 방법에 대한 글을 썼는데 읽어볼 만합니다. 만약 Webpack을 사용하고 있다면, [LQIP 로더](https://lqip-loader.firebaseapp.com/)를 사용해 전환 작업의 일부를 자동화해볼 수 있습니다.

실제 상황에서는 보고 싶은 고화질 사진을 찾아본 후에 스크롤을 내려버릴 수도 있습니다. 대부분의 경우, 웹사이트가 한 번에 소수의 이미지만 전체 화질로 불러오고 나머지는 플레이스홀더 색상이나 이미지로 채워두는 것을 보게 될 것입니다. 계속 스크롤링하면 플레이스홀더 이미지가 전체 화질 이미지로 대체될 것입니다. 이때가 바로 레이지 로딩이 실제로 작동되는 중입니다.

최근 회자가 되는 기법은 픽셀이 아니라 *벡터*를 기반으로 한 저품질 이미지를 미리보기용으로 사용하는 것인데, Tobias Baldauf가 자신의 툴 [SQIP](https://github.com/technopagan/sqip)에서 처음 선보인 기법입니다. [Primitive](https://github.com/fogleman/primitive) 유틸리티를 사용해 원본 이미지 내에 보이는 특정적인 형태에 따라 몇 가지 간단한 SVG 도형으로 그 형태를 흉내 내 미리보기 이미지를 생성한 후, [SVGO](https://github.com/svg/svgo)로 SVG를 최적화한 다음에 마무리로 가우시안 블러 필터를 추가합니다. 이렇게 하면 용량이 800에서 1000바이트 내외인 SVG 플레이스홀더 이미지를 만들 수 있습니다. 모든 화면에서 선명해 보이면서 이미지 콘텐츠에 대한 힌트까지 줄 수 있는 플레이스홀더 입니다. 당연한 것 같긴 하지만, 레이지 로딩과 저품질 이미지 미리보기 기법 두 개를 [혼합하여 같이](https://calendar.perfplanet.com/2017/progressive-image-loading-using-intersection-observer-and-sqip/) 사용할 수도 있습니다.

**페이지에다 레이지 로딩을 어떻게 적용하나요?**

레이지 로딩용 플러그인과 기법이 몇 가지 있습니다. 저는 Alexander Farkas가 만든 [lazysizes](https://github.com/aFarkas/lazysizes) 사용을 추천합니다. 성능과 기능 면에서 모두 우수하고, [Intersection Observer](https://developers.google.com/web/updates/2016/04/intersectionobserver)와의 통합이 선택적으로 가능하며 플러그인 사용 또한 지원하기 때문입니다.

**Lazysizes로 무엇을 할 수 있나요?**

Lazysizes는 자바스크립트 라이브러리입니다. 환경설정은 별도로 할 필요가 없습니다. 압축된(minify) js 파일을 내려받아서 웹페이지에 넣으세요.

다음 코드 예시는 README 파일에서 발췌해 온 것입니다.

이미지, 혹은 iframe에 ‘lazyload’ 클래스를 추가하고 여기에 `data-src` 혹은/그리고 `data-srcset` 속성을 같이 써주세요.

또한, 옵션으로 src 속성을 사용해 저품질 이미지를 추가해 놓을 수도 있습니다.

```html
<!-- 비-반응형: -->
<img data-src="image.jpg" class="lazyload" />

<!-- 자동 크기 계산이 적용된 반응형 예시: -->
<img
    data-sizes="auto"
    data-src="image2.jpg"
    data-srcset="image1.jpg 300w,
    image2.jpg 600w,
    image3.jpg 900w" class="lazyload" />

<!-- iframe 예시 -->
<iframe frameborder="0"
    class="lazyload"
    allowfullscreen=""
    data-src="//www.youtube.com/embed/ZfV-aYdU4uE">
</iframe>
```

저는 이 책 웹사이트 버전에 Lazysizes(이것 말고 다른 라이브러리를 사용하셔도 됩니다)를 Cloudinary와 함께 사용하여 사용자 주문형(on-demand) 반응형 이미지를 구현해 두었습니다. 덕분에 크기, 품질, 포맷을 각기 달리해서 자유롭게 실험해 볼 수 있었습니다. 최소한의 노력을 들여서 이미지 점진적 로딩 여부에 대한 실험도 역시 해볼 수 있었습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502501095/essential-image-optimization/cloudinary-responsive-images.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502501095/essential-image-optimization/cloudinary-responsive-images.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502501095/essential-image-optimization/cloudinary-responsive-images.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502501095/essential-image-optimization/cloudinary-responsive-images.jpg"
        alt="Cloudinary supports on-demand control of image quality, format and several other features."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502501095/essential-image-optimization/cloudinary-responsive-images.jpg"/>
</noscript>
</picture>
</figure>

**Lazysizes 라이브러리에 포함된 기능**

* 현재 및 앞으로 불러올 레이지 로딩 요소에 대한 가시적인 변화를 자동 탐지.
* 표준 반응형 이미지 지원(picture와 srcset).
* 이미지 크기 자동 계산 및 미디어 쿼리 기능에 별명(alias name) 부여 기능.
* CSS 혹은 JS 코드 용량이 많은 페이지나 웹앱에서 수백 개의 이미지 및 iframe를 대상으로 사용 가능.
* 확장성: 플러그인 사용을 지원.
* 가볍고 기술적 성숙도가 높은 솔루션.
* 향상된 SEO: 이미지나 에셋을 크롤러로부터 숨기지 않습니다.

**이 외의 레이지 로딩 관련 옵션**

Lazysizes만이 유일한 선택지는 아닙니다. 아래 목록에 몇 가지 레이지 로딩 라이브러리를 나열해 보았습니다.

*   [Lazy Load XT](http://ressio.github.io/lazy-load-xt/)
*   [BLazy.js](https://github.com/dinbror/blazy) (or [Be]Lazy)
*   [Unveil](http://luis-almeida.github.io/unveil/)
*   [yall.js (Yet Another Lazy Loader)](https://github.com/malchata/yall.js): 용량이 1KB 이하인 라이브러리. 가능한 곳에서는 Intersection Observer를 사용합니다.

**레이지 로딩 기법의 문제점은 무엇이 있나요?**

*   자바스크립트로 이미지 조작을 하므로 스크린 리더기와 몇몇 서치봇, 그리고 자바스크립트 사용이 불가능한 사용자들은 레이지 로딩되는 이미지를 볼 수 없게 될 것입니다. 그러나 `<noscript>`로 대비를 해 놓는다면, 우회적으로 어느정도 해결이 가능한 문제입니다.
*   이미지 레이지 로딩 시점을 정하는 스크롤 리스너 때문에 브라우저의 스크롤링 성능에 역영향을 끼칠수도 있습니다. 그렇게 되면 브라우저가 여러번 요소를 그려내기 때문에 크롤링 속도가 느려지게 됩니다. 그러나 똑똑한 레이지 로딩 라이브러리들은 이런 부작용에 대비하여 병목현상(throttling)을 이용합니다. Intersection Observer를 사용하는 것이 한가지 방법인데, lazysizes 라이브러리에서 사용을 지원합니다.

이미지 레이지 로딩은 대역폭 비용을 줄이고 사용성을 향상하기 위해 많은 곳에서 사용하고 있는 패턴입니다. 여러분의 서비스에 적용해도 괜찮을지 스스로 평가해 보세요. 더 자세한 정보가 필요하다면 '[이미지 레이지 로딩하기](https://jmperezperez.com/lazy-loading-images/)'와 '[Medium의 점진적 로딩 기법 구현하기](https://jmperezperez.com/medium-image-progressive-loading-placeholder/)' 글을 참조하세요.

## <a id="display-none-trap" href="#display-none-trap"><code>display: none;</code> 함정 피하기</a>

여태까지의 반응형 이미지 사용 방식은 CSS의 `display` 속성을 사용하면 브라우저가 이미지 요청을 어떻게 다루는지에 대한 이해가 부족했습니다. 때문에 이미지 요청이 예상했던 것보다 훨씬 더 많이 발생할 가능성을 높였으며, 이 때문에 반응형 이미지 로딩용으로 `<picture>`나 `<img srcset>`에 대한 선호되고 있는 추세입니다.

특정 미디어 쿼리 중단점(breakpoint)에서 이미지가 `display:none` 처리 되도록 하는 코드를 작성해 보신 적이 있나요?

```html
<img src="img.jpg">
<style>
@media (max-width: 640px) {
    img {
        display: none;
    }
}
</style>
```

아니면 `display:none` 클래스를 사용해 이미지 노출을 제어해 본 적은요?

```html
<style>
.hidden {
  display: none;
}
</style>
<img src="img.jpg">
<img src=“img-hidden.jpg" class="hidden">
```

구글 크롬 개발자도구의 네트워크 패널에서 간단하게 검사를 해보면, 위의 방식으로 숨긴 이미지가 여전히 페칭(fetching)되고 있음을 확인해 볼 수 있습니다. 심지어 가져오리라 생각하지 못했던 이미지 까지도요. 브라우저의 이런 행동방식은 사실 임베디드 리소스 명세에 맞게 제대로 구현된 것입니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1503260160/essential-image-optimization/display-none-images.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1503260160/essential-image-optimization/display-none-images.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503260160/essential-image-optimization/display-none-images.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503260160/essential-image-optimization/display-none-images.jpg"
        alt="Images hidden with display:none still get fetched"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1503260160/essential-image-optimization/display-none-images.jpg"/>
</noscript>
</picture>
</figure>

**`display:none` 때문에 이미지 `src` 요청은 안 일어나지 않나요?**

```html
<div style="display:none"><img src="img.jpg"></div>
```

아닙니다. `src` 속성값으로 들어가는 이미지 요청은 여전히 발생하게 됩니다. 자바스크립트가 소스 이미지를 바꿔 버리기 전에 이미지 요청이 일어나기 때문에, `display:none`에 의존하여 라이브러리를 사용하거나 만들 수는 없습니다.

**`display:none`를 사용하면 `background: url()` 요청 발생은 피할 수 있지 않나요?**

```html
<div style="display:none">
  <div style="background: url(img.jpg)"></div>
</div>
```

네. 요소 파싱(parsing) 작업이 끝나자마자 CSS 배경을 가져오는 작업이 일어나지는 않습니다. `display:none`이 적용된 자식 요소들에 대한 CSS 스타일을 계산하는 일은 해봤자 문서 렌더링에 영향이 없기 때문에 그다지 쓸모가 없는 작업입니다. 그래서 그런 자식 요소들의 배경 이미지애 대한 다운로드 또는 계산 작업 역시 진행되지 않습니다.

제이크 아치볼드(Jake Archibald)가 만든 [리퀘스트 퀘스트(Request Quest, 요청 탐구)](https://jakearchibald.github.io/request-quest/)에 들어가 보시면, 반응형 이미지 로딩에서 `display: none`을 사용할 때 빠지기 쉬운 함정에 대한 근사한 퀴즈를 풀어보실 수 있습니다. 특정 브라우저가 이미지 요청 로딩을 어떻게 처리하는지 의문이 생기신다면, 그 브라우저의 개발자 도구를 열어 직접 확인해 보세요.

다시 말하지만, 가능하다면 `display:none`에 의존하기 보다는 `<pictrue>`와 `<img srcset>`을 사용해 주세요.

## <a id="image-processing-cdns" href="#image-processing-cdns">이미지 처리 CDN을 사용해도 될까요?</a>

*대부분의 경우, 나만의 이미지 처리 파이프라인을 구축해 보겠다고 블로그 포스팅을 읽어보고 설정을 변경하는 데 들이는 시간을 돈으로 따져보면 CDN 서비스 이용료보다 훨씬 많은 비용이 들어갑니다. 이미지 처리 자동화에 사용할 만한 옵션이 충분히 존재하는데, 무료 서비스를 사용하고 싶다면 [Cloudinary](http://cloudinary.com/), 무료 시험판을 사용해 보고 싶다면 [Imgix](https://www.imgix.com/), 유료 서비스 대신 사용할 수 있는 오픈소스 소프트웨어(OSS)를 찾으신다면 [Thumbor](https://github.com/thumbor/thumbor)가 있습니다.*

페이지 로딩 시간을 최적화 시키려면 이미지 로딩을 최적화 시켜야 합니다. 최적화를 제대로 하려면 반응형 이미지 사용 전략을 잘 짜야하며, 이를 통해 서버 쪽 이미지 압축, 이미지 최적 포맷 자동 선택 및 반응형 이미지 크기 자동 재조정을 할 수 있습니다. 중요한 것은 각각의 사용자 기기에 알맞게 적절한 크기의 이미지를 적절한 해상도로 할 수 있는 한 빨리 전달하는 것입니다. 혹자는 쉽다고 생각할 지도 모르나 그렇지 않습니다.

**서버 사용 vs. CDN 사용**

이미지 조작 기술은 워낙 복잡하고 계속해서 신기술이 나오고 있는 환경이기 때문에, 실무 경험을 갖춘 어떤 분의 말을 먼저 인용한 후, 제안으로 넘어가 보겠습니다.

"담당하고 계신 제품이 이미지 조작 기술을 주로 하는 것이 아니라면, 직접 서버를 운용하실 생각은 하지 말아주세요. Cloudinary(혹은 imigix, Ed.)와 같은 서비스를 사용하시는 편이 여러분이 직접 하시는 것보다 훨씬 더 효율적이고 나은 결과를 가져다 줄 것이므로, 이런 서비스들을 사용하세요. 그리고 만약 서비스 사용 비용 걱정이 든다면, 여러분이 직접 이를 개발하고 유지보수하는 데 쓰일 비용, 호스팅, 저장, 그리고 전송 비용까지 전부 합쳐 얼마나 들지 한번 생각해 보세요." - [Chris Gmyr](https://medium.com/@cmgmyr/moving-from-self-hosted-image-service-to-cloudinary-bd7370317a0d)

현재로써는 위 인용구에 대해 동의를 표하는 바이며, 여러분도 이미지 처리하실 때 CDN 사용을 고려해 보시기를 추천합니다. CDN 서비스 두개를 가지고 앞에서 얘기한 작업들을 처리할 때 어떤 점이 다를지 비교 분석 해보도록 하겠습니다.

**Cloudinary와 imgix**

[Cloudinary](http://cloudinary.com/)와 [imgix](https://www.imgix.com/) 둘 다 저명한 이미지 처리 CDN 입니다. 전세계 수십만명이 넘는 개발자들과 넷플릭스와 레드불을 포함한 회사들이 채택한 CDN입니다. 좀 더 자세히 알아보도록 하겠습니다.

**가장 먼저 알아두어야 할 것은 뭔가요?**

여러분이 CDN 서비스처럼 네트워크 서버의 주인이 아닌 이상, 직접 서버를 운용하는 것보다 이들 CDN 서비스를 사용함으로써 얻을 수 있는 가장 큰 이점은 바로 이들이 운용하는 전세계에 분산된 네트워크 시스템을 이용하여 사용자마다 가장 근처에 위치한 서버에서 이미지 사본을 받아볼 수 있게 할 수 있다는 것입니다. 그리고 이미지 로딩 전략 트렌드가 바뀔 때마다 이에 대한 '미래 예측(future proof)'도 여러분보다 CDN 서비스에서 훨씬 더 쉽게 할 수 있습니다. 만약 이를 직접 하게된다면 유지보수도 하면서 신흥 포맷에 대한 브라우저 지원 여부 및 이미지 압축 커뮤니티 소식도 계속해서 따라잡아야 합니다.

두번째 장점은 각각의 서비스마다 가격 플랜이 단계별로 책정되어 있다는 것입니다. Cloudinary는 [무료](http://cloudinary.com/pricing) 플랜도 제공하며, imgix는 대용량 프리미엄 플랜에 부과하는 가격과 비교했을 때 비싸지 않은 가격으로 표준 플랜을 마련해 두었습니다. Imgix에서는 서비스에 대한 보증 차원에서, Cloudinary의 무료 플랜과 거의 동일한 급의 무료 [시험](https://www.imgix.com/pricing)판도 제공합니다.

세번째 장점은 두 서비스 모두 API를 통한 접근을 제공한다는 점입니다. 개발자들은 프로그램을 통해 CDN에 접속하여 이미지 처리 과정을 자동화시킬 수 있습니다. 클라이언트 라이브러리, 프레임워크 플러그인, API 문서 모두 제공합니다. 이 중 일부 기능은 높은 가격 플랜 사용자들에게만 제공됩니다.

**이미지 처리를 시작해 봅시다**

지금은 정적인 이미지만 다뤄보도록 하겠습니다. Cloudinary와 Imgix 둘 다 다양한 이미지 조작 기능을 제공하며, 표준 및 무료 플랜에서도 압축, 크기 조정, 자르기 및 썸네일 생성과 같은 중요 기능을 지원합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1502426282/essential-image-optimization/Modern-Image36.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1502426282/essential-image-optimization/Modern-Image36.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image36.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image36.jpg"
        alt="cloudinary media library"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1502426282/essential-image-optimization/Modern-Image36.jpg"/>
</noscript>
</picture>
<figcaption>Cloudinary 미디어 라이브러리: Cloudinary는 기본 옵션으로 [비-점진적 JPEG 이미지](http://cloudinary.com/blog/progressive_jpegs_and_green_martians) 인코딩을 제공합니다. 이들 이미지 생성을 하려면 '옵션 더보기'에서 '점진적'을 체크하거나 'fl_progressive' 플래그를 전달해 주세요.</figcaption>
</figure>

Cloudinary에서는 [이미지 변형 카테고리를 7가지 대분류로](http://cloudinary.com/documentation/image_transformations), 총 48가지의 하위 범주로 제공합니다. Imgix에서는 [100가지 이상의 이미지 처리 작업을 제공한다고](https://docs.imgix.com/apis/url?_ga=2.52377449.1538976134.1501179780-2118608066.1501179780) 선전하고 있습니다.

**기본 제공 사항은 뭔가요?**

*   Cloudinary에서는 다음과 같은 최적화 작업을 기본으로 제공합니다.
*   [MozJPEG을 사용한 JPEG 이미지 인코딩](https://twitter.com/etportis/status/891529495336722432) (Guetzli 대신 기본 옵션으로 선택됨)
*   변형 후의 이미지 파일에서 모든 관련 메타데이터 제거(원본 파일은 건드리지 않음). 이렇게 말고 메타데이터를 건드리지 않은 채 이미지를 변형시키고 싶다면 keep_iptc 플래그를 추가해 주세요.
*   WebP, GIF, JPEG, JPEG-XR 포맷 자동 품질로 생성 가능. 기본 품질 조정을 덮어 쓰고 싶다면 품질 파라미터를 조정해 주세요.
*   PNG, JPEG, GIF 포맷 이미지 생성시 이미지 시각 품질에는 영향을 최소한으로 주면서 파일 사이즈를 최대한 줄일 수 있도록 [최적화](http://cloudinary.com/documentation/image_optimization#default_optimizations) 알고리즘 실행.

Imgix는 Cloudinary처럼 기본적으로 제공하는 최적화 기능이 없습니다. 대신 기본 이미지 품질 설정을 변경할 수 있습니다. Imgix를 사용하신다면 자동 파라미터 기능을 사용해 여러분의 이미지 카탈로그 기준 최적화 레벨을 자동으로 설정할 수 있습니다.

현재, 자동 파라미터 설정 방법은 [네가지](https://docs.imgix.com/apis/url/auto)가 있습니다.

*   압축
*   시각적 향상
*   파일 포맷 변환
*   적목현상 제거

Imgix에서는 JPEG, JPEG2000, PNG, GIF, Animated GIF, TIFF, BMP, ICNS, ICO, PDF, PCT, PSD, AI 포맷을 지원합니다.

Cloudinary는 JPEG, JPEG 2000, JPEG XR, PNG, GIF, Animated GIF, WebP, Animated WebP,BMPs, TIFF, ICOs, PDF, EPS, PSD, SVG, AI, DjVu, FLIF, TARGA 포맷을 지원합니다.

**성능은 어떤가요?**

CDN 전송 성능에 영향을 주는 것은 [지연(latency)](https://docs.google.com/a/chromium.org/viewer?a=v&pid=sites&srcid=Y2hyb21pdW0ub3JnfGRldnxneDoxMzcyOWI1N2I4YzI3NzE2)과 속도가 거의 전부입니다.

완전히 이미지가 캐싱되지 않으면 거의 항상 지연 시간이 늘어납니다. 그러나 이미지가 일단 한번 캐싱된 후에 네트워크 서버에 배포가 된다면, 글로벌 CDN에서는 사용자와 가장 가까운 전달 방법을 찾아낼 수 있으며, 적절하게 처리된 이미지 덕분에 전송량도 줄일 수 있습니다. 제대로 이미지 처리를 하지 않았을 때, 또는 오직 한대의 서버만 가지고 지구 전체를 커버하려고 할 때와 글로벌 CDN을 사용하는 경우를 비교해 보면, 후자의 경우 거의 대부분의 지연 문제를 완화시킬 수 있습니다.

두 서비스 모두 속도도 빠르고 여기저기 널리 퍼져있는 CDN을 사용하고 있습니다. 이렇게 CDN을 설정해 둔 덕에 지연 속도는 줄어들면서 다운로드 속도는 증가하게 됩니다. 다운로드 속도는 페이지 로딩 시간에 영향을 주기도 하며, 그렇기 때문에 사용자 경험과 전환률을 측정해 볼 때 가장 중요한 단위 중 하나에 속합니다.

**그래서 둘을 비교해 보면요?**

Cloudinary를 사용중인 고객수는 [16만명](http://cloudinary.com/customers) 정도이며, 넷플릭스, 이베이, 드롭박스도 여기에 속합니다. Imgix는 보유고객수를 공개하지 않았으나 Cloudinary 보다는 적습니다. 그렇다고는 해도, Kickstarter, Exposure, unsplash, Eventbrite와 같이 이미지를 평균 이상으로 많이 사용하는 회사들이 imgix를 사용하고 있습니다.

이미지 조작 과정에는 제어할 수 없는 변수들이 아주 많이 포진해 있기 때문에, 두 서비스의 성능을 일대일로 정확하게 비교해 보는 것은 어렵습니다. 이미지 처리를 얼마나 해야 하는지(이에 따라 소요되는 시간이 상당히 달라집니다), 그리고 최종 출력물의 크기와 해상도(속도와 다운로드 시간에 영향을 주죠)에 따라 성능이 아주 크게 달라집니다. 아마 비용이 여러분에게 제일 중요한 최후의 요인이 될 것입니다.

CDN을 사용하려면 돈이 필요합니다. 이미지가 많이 올라가면서 트래픽이 많은 사이트는 한달 CDN 사용료만 해도 수백달러가 넘어가는 비용이 들어갈 겁니다. CDN 서비스를 최대한 잘 사용하려면 미리 관련 지식과 프로그래밍 스킬을 일정 수준 이상으로 닦아놓아야 합니다. 너무 오버스러운 기능만 사용하지 않으면 CDN을 사용하면서 곤경에 처할 일은 아마 전혀 없을 겁니다.

그러나 만약 이미지 처리 도구나 API를 사용하면서 어려움을 겪으신다면, 지금 내가 러닝 커브를 겪고 있구나 생각하시면 됩니다. CDN 서버를 제대로 사용하려면 여러분 로컬 링크 URL을 일부 바꿔야 할 수도 있습니다. 이미지 자산(asset) 관리 제대로 해두세요 :)

**결론**

만약 현재 스스로 이미지 서버를 구축해서 사용중이거나 그럴 계획이 있으시다면, CDN을 사용하면 어떨지 한번 고려해 볼 필요가 있습니다.

## <a id="caching-image-assets" href="#caching-image-assets">이미지 에셋 캐싱하기</a>

[HTTP 캐시 헤더](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching#cache-control)를 사용해 서비스 리소스에 대한 캐시 정책을 표기해 둘 수 있습니다. 구체적으로 말하자면, `Cache-Control`을 사용해 누가 응답을 캐시해 놓고 캐시 기간을 얼마나 될지 정의할 수 있습니다.

여러분이 유저에게 전달하는 이미지 대부분은 정적인 에셋이라서 그 내용이 앞으로도 [변하지 않는](http://kean.github.io/post/image-caching) 에셋입니다. 이런 유형의 에셋에 가장 적합한 캐시 전략은 '과감한(aggressive) 캐싱'입니다.

HTTP 캐싱 헤더를 설정할 때 `Cache-Control`의 `max-age`를 1년 내의 값 중 가장 최대로 줍니다. (예: `Cache-Control:public; max-age=31536000`) 이런식의 과감한 캐싱은 대부분의 이미지 유형에 적합하며, 특히 아바타나 이미지 헤더와 같이 오랫동안 사용할 이미지에 적당합니다.

<aside class="note"><b>알아두기:</b> 만약 이미지를 전달하는 데 PHP를 사용중이라면, 기본 [session_cache_limiter](http://php.net/manual/en/function.session-cache-limiter.php) 설정 때문에 캐시가 삭제될 수도 있습니다. 이미지 캐싱에 있어서 이런 일은 재앙과도 같으므로, session_cache_limiter('public')로 세팅하면 이런 일을 [피할 수]((https://stackoverflow.com/a/3905468)) 있습니다. 이를 비활성화 시키고 커스텀 `Cache-Control`을 설정하는 것도 괜찮은 방법입니다.</aside>

## <a id="preload-critical-image-assets" href="#preload-critical-image-assets">중요한 이미지는 프리로딩(preloading)</a>

중요한 이미지 에셋들은 [`<link rel=preload>`](https://www.w3.org/TR/preload/)를 사용해 프리로딩할 수 있습니다.

`<link rel=preload>`은 선언적인(declarative) 자산 가져오기 방식이며, 브라우저가 `document`의 `onload` 이벤트로 인한 방해를 받지 않고 해당 리소스 요청을 할 수 있도록 강제할 수 있는 방법입니다. 이를 통해 문서 파싱 과정이 끝날때까지 존재감이 없었을 뻔한 리소스에 대한 요청 우선순위를 높일 수 있습니다.

이미지는 `as` 속성값을 `image`로 명시하여 프리로딩할 수 있습니다.

```html
<link rel="preload" as="image" href="logo.jpg"/>
```

`<img>`, `<picture>`, `srcset`에 들어갈 이미지 리소스 및 SVG 파일 모두 이 최적화 방식을 적용할 수 있습니다.

<aside class="note"><b>알아두기:</b> `<link rel="preload">`는 [Safari Tech Preview](https://developer.apple.com/safari/technology-preview/release-notes/), 크롬, 그리고 오페라와 같이 블링크 기반의 브라우저에서 [지원](http://caniuse.com/#search=preload)되며, 파이어폭스에서는 [구현이 진행중]((https://bugzilla.mozilla.org/show_bug.cgi?id=1222633))입니다.</aside>

[Philips](https://www.usa.philips.com/), [Flipkart](https://www.flipkart.com/), [Xerox](https://www.xerox.com/) 같은 사이트에서는 `<link rel=preload>`를 사용해 메인 로고 에셋(이들 이미지는 웹 페이지 앞쪽에서 자주 사용됩니다)을 미리 불러오고 있습니다. [Kayak](https://kayak.com/) 역시 헤더에 들어갈 히어로 이미지가 최대한 빠르게 로딩될 수 있도록 하기 위해 프리로드를 사용하고 있습니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504057647/essential-image-optimization/preload-philips.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1504057647/essential-image-optimization/preload-philips.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504057647/essential-image-optimization/preload-philips.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504057647/essential-image-optimization/preload-philips.jpg"
        alt="Philips use link rel=preload to preload their logo image"
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504057647/essential-image-optimization/preload-philips.jpg"/>
</noscript>
</picture>
</figure>

**Link 프리로드 헤더가 뭐죠?** 

프리로드 링크는 HTML 태그로도 표기가 가능하고, [HTTP Link 헤더](https://www.w3.org/wiki/LinkHeader)로도 표기할 수 있습니다. 두 방법 모두 프리로드 링크가 브라우저가 특정 리소스를 로딩할 때 메모리 캐시에서 먼저 시작하도록 만듭니다. 프리로드 스캐너나 파서가 그 리소스를 발견할 때까지 기다릴 필요 없이 페이지에서 사용할 수 있다고 자신할 수 있음을 나타냅니다.

이미지 Link 프리로드 헤더는 다음과 비슷하게 생겼습니다.

```
Link: <https://example.com/logo-hires.jpg>; rel=preload; as=image
```

파이낸셜 타임스에서는 Link 프리로드 헤더를 사이트에 도입했더니, 발행인란 이미지 표시할 때 시간을 [1초 단축](https://twitter.com/wheresrhys/status/843252599902167040)시킬 수 있었다고 합니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1504055773/essential-image-optimization/preload-financial-times.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1504055773/essential-image-optimization/preload-financial-times.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504055773/essential-image-optimization/preload-financial-times.jpg" />

<img
        class="lazyload"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504055773/essential-image-optimization/preload-financial-times.jpg"
        alt="The FT using preload. Displayed are the WebPageTest before and after traces showing improvements."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1504055773/essential-image-optimization/preload-financial-times.jpg"/>
</noscript>
</picture>
<figcaption>하단: `<link rel=preload>` 사용, 상단: 사용 안함. WebPageTest의 3G Moto G4 환경에서 테스트한 [적용 전](https://www.webpagetest.org/result/170319_Z2_GFR/)과 [적용 후](https://www.webpagetest.org/result/170319_R8_G4Q/).</figcaption>
</figure>

이와 유사하게 위키피디아에서도 그들의 [사례 연구](https://phabricator.wikimedia.org/phame/post/view/19/improving_time-to-logo_performance_with_preload_links/) 글에서 다루었듯이, Link 프리로드 헤더를 사용해 성능 개선을 이뤘다고 합니다.

**이 방법을 사용할 때 주의할 점은 무엇이 있나요?**

해당 이미지 에셋이 프리로딩 할 만한 가치가 정말 있는지 확신이 있어야 합니다. 이미지가 사용자 경험에 있어서 별로 중요한 것이 아닌 경우, 이 대신 여러분의 노력을 필요로 하는, 좀 더 빨리 불러올 만한 가치가 있는 다른 콘텐츠가 있을 겁니다. 이미지 요청에 대한 우선순위를 앞세우면, 다른 리소스에 대한 요청 우선순위가 큐의 저 아래로 밀려나게 됩니다.

브라우저 지원 범위가 넓지 않은 이미지 포맷(예: WebP)을 `rel=preload`를 사용해 프리로드 하면 안됩니다. 또한 `srcset`로 정의된 반응형 이미지를 사용하는 경우, 디바이스 조건에 따라 전달 소스가 달라지게 되므로 Link 프리로드 사용을 하지 않는 편이 좋습니다.

프리로딩에 대해 더 알아보고 싶다면, [크롬 브라우저에서의 프리로드, 프리패치, 그리고 우선순위에 관하여](https://medium.com/reloading/preload-prefetch-and-priorities-in-chrome-776165961bbf)와 [프리로드: 어디에 좋은 건가요?](https://www.smashingmagazine.com/2016/02/preload-what-is-it-good-for/) 글을 읽어보세요.

## <a id="performance-budgets" href="#performance-budgets">이미지 성능 예산 짜기</a>

성능 예산이란, 웹 페이지 성능에 대한 '예산'을 뜻하며 그 페이지를 담당하는 팀은 이 예산을 넘지 않도록 해야 합니다. 예를 들어, '어떤 페이지에서도 이미지 용량이 200KB를 넘으면 안된다'라거나 '사용자가 페이지를 접하여 사용하기 까지의 시간은 3초 이하여야만 한다' 등을 성능 예산으로 정해 둘 수 있습니다. 예산을 초과한다면, 원인을 찾아보고 목표 예산을 맞추려면 어떻게 해야 하는지에 대해서도 알아보세요.

사이트 이해 관계자들과 성능에 관해 논의할 때, 예산은 유용한 기준틀이 되어줄 수 있습니다. 디자인 혹은 비즈니스적인 결정으로 인해 사이트 성능에 영향이 갈 것 같으면, 예산을 측정해 보세요. 내려진 결정이 사이트 사용자 경험을 헤칠 것 같으면, 예산을 참고로 하여 결정을 유보시키거나 다시 한번 생각해 보도록 만들 수 있습니다.

제가 관찰한 바로는, 성능 예산 모니터링을 자동화 시켜둔 경우에 팀 성과가 가장 좋았습니다. 예산이 어디서 틀어졌는지 알아보기 위해 네트워크 폭포수(waterfall)을 일일이 직접 확인하기 보다는, 이를 자동화 시켜 확인하면 예산 초과시 알림을 받아볼 수 있습니다. 성능 예산 추적에 유용한 서비스로는 [Calibre](https://calibreapp.com/docs/metrics/budgets)와 [SpeedCurve](https://speedcurve.com/blog/tag/performance-budgets/)가 있습니다.

이미지 크기에 대한 성능 예산을 일단 정해 놓으면, SpeedCurve에서는 모니터링을 하기 시작해서 예산이 초과되었을 경우 여러분에게 알림을 보내줍니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1505805372/essential-image-optimization/F2BCD61B-85C5-4E82-88CF-9E39CB75C9C0.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1505805372/essential-image-optimization/F2BCD61B-85C5-4E82-88CF-9E39CB75C9C0.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1505805372/essential-image-optimization/F2BCD61B-85C5-4E82-88CF-9E39CB75C9C0.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1505805372/essential-image-optimization/F2BCD61B-85C5-4E82-88CF-9E39CB75C9C0.jpg"
        alt="SpeedCurve image size monitoring."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1505805372/essential-image-optimization/F2BCD61B-85C5-4E82-88CF-9E39CB75C9C0.jpg"/>
</noscript>
</picture>
</figure>

Calibre 역시 비슷한 기능을 제공하는데, 목표 디바이스 별 예산 설정 기능도 지원합니다. 모바일 성능 예산에 따라 데스크톱용부터 Wifi용 이미지 사이즈에 대한 예산이 크게 차이날 수 있기 때문에 유용하게 사용될 수 있는 기능입니다.

<figure>
<picture>
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_500/v1505805371/essential-image-optimization/budgets.jpg"
        media="(max-width: 640px)" />
<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/c_scale,w_900/v1505805371/essential-image-optimization/budgets.jpg"
        media="(max-width: 1024px)" />

<source
        data-srcset="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1505805371/essential-image-optimization/budgets.jpg" />

<img
        class="lazyload small"
        data-src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1505805371/essential-image-optimization/budgets.jpg"
        alt="Calibre supports budgets for image sizes."
         />
<noscript>
  <img src="https://res.cloudinary.com/ddxwdqwkr/image/upload/v1505805371/essential-image-optimization/budgets.jpg"/>
</noscript>
</picture>
</figure>

## <a id="closing-recommendations" href="#closing-recommendations">글을 마치며 추천 사항 몇 가지</a>

결국엔 이미지 최적화 전략 선택은 사용자에게 전달할 이미지 타입에 따라 결정됩니다. 그리고 여러분이 정해 놓은 기준이 바로 일련의 합리적인 평가 기준인 셈입니다. 평가 기준으로 SSIM이나 Butteraugli를 사용할 수도 있으며, 만약 작은 크기의 이미지만 사용중이라면, 무엇을 적합한 기준으로 삼을 지 사람의 눈으로 판별하기는 어려울 것입니다.

**마치면서, 제 추천 사항을 말하자면 다음과 같습니다.**

브라우저에 따라 이미지 포맷을 조건적으로 사용자에게 제공할 수 **없는** 환경이라면 다음을 추천합니다. 

* JPEG 품질 90 이상: Guetzli + MozJPEG의 jpegtran 조합을 추천합니다.
    * 웹에서는 `q=90`은 쓸데없이 높은 품질입니다. `q=80`으로도 충분하며, 레티나 디스플레이에서조차 `q=50`를 사용할 수 있습니다. Guetzli에서는 그렇게 낮게 품질을 설정할 수 없으므로, 웹용으로 MozJPEG을 사용하시면 됩니다.
    * Kornel Lesi&#x144;ski는 최근 넓은 색 영역 디스플레이에서도 크롬 디스플레이의 자연색을 잘 보이게 하기 위해 소량의 sRGB 프로파일을 MozJPEG의 cjpeg 커맨드에 넣었습니다.
* PNG pngquant + advpng 조합은 압축률이 꽤 좋습니다.

브라우저에 따라 조건적으로 이미지를 전달할 수 **있다면** 다음을 추천합니다. (`<picture>`, [Accept header](https://www.igvita.com/2013/05/01/deploying-webp-via-accept-content-negotiation/) 혹은 [Picturefill](https://scottjehl.github.io/picturefill/)을 사용하는 경우)

* WebP를 지원하는 브라우저에는 해당 포맷 파일을 전달합니다.
  * 원본 품질 100% 이미지로 WebP 이미지를 만듭니다. 이렇게 하지 않으면 JPEG 왜곡*과* WebP 왜곡이 동시에 발생한 것 같은 이미지를 브라우저에게 주는 꼴이 되어버립니다! 만약 비압축 소스 이미지를 WebP로 압축한다면, 눈에는 잘 보이지 않는 WebP 왜곡이 발생하게 될 것이며 압축률 역시 더 나을 겁니다.
  * 압축률 최적화를 하는 경우, WebP 팀이 사용하는 기본 설정인 `-m 4 -q 75`은 대부분의 경우에 잘 맞습니다.
*  또한 WebP에는 모든 매개 변수 조합을 탐색하여 파일을 가장 작은 크기로 줄일 수 있는 무손실 특수 모드(`-m 6 -q 100`)가 있습니다. 속도가 한 자리수 정도 더 느릴 수 있으나, 정적인 에셋용으로는 충분히 사용할 가치가 있습니다.
* 타 브라우저용 폴백 이미지로는 Guetzli/MozJPEG로 압축한 이미지 소스를 전달하시면 됩니다.

즐거운 압축 되세요!(Happy compressing!)

<aside class="note"><b>알아두기:</b> 이미지 최적화 방법에 대한 조금 더 실용적인 가이드를 보고 싶다면, 제레미 와그너(Jeremy Wagner)의 책인 [실전 웹성능(Web Performance in Action)](https://www.manning.com/books/web-performance-in-action)을 강력 추천합니다. [고성능 이미지(High Performance Images)](http://shop.oreilly.com/product/0636920039730.do) 책 역시 동일 주제에 대해 살짝 다르기는 하나 역시 뛰어난 조언들로 채워져 있습니다.</aside>

## <a id="trivia" href="#trivia">트리비아</a>

* [JPEG XT](https://jpeg.org/jpegxt/)는 1992년에 만들어진 JPEG 명세서의 확장판입니다. 예전 JPEG를 기반으로 확장판을 구현해 픽셀 렌더링을 완벽하게 하기 위해서는 구 1992년판 명세를 명확하게 재정의 했어야 했고, [libjpeg-turbo](https://libjpeg-turbo.org/)가 (그당시 인기도를 바탕으로) JPEG XT의 레퍼런스로 채택되었습니다.
* [PIK](https://github.com/google/pik)는 눈여겨 볼만한 새로운 이미지 코덱입니다. JPEG과 호환이 되며 좀 더 효율적으로 색공간을 관리합니다. 그리고 Guetzli와 유사한 장점들을 지니고 있습니다. 디코딩 하는데 JPEG 속도의 2/3 만큼 걸리며, libjpeg 보다 파일 용량을 54% 가량 더 절약할 수 있습니다. Guetzli화 된 JPEG보다 디코딩, 압축 속도가 모두 빠릅니다. 최신 이미지 코덱에 대한 심리지각 유사성에 관한 [연구](https://encode.ru/threads/2814-Psychovisual-analysis-on-modern-lossy-image-codecs)에 따르면, PIK가 다른 대조군들보다 크기가 절반 이상 작습니다. 불행하게도, 아직 이 코덱을 사용하기에는 시기상조이며 인코딩 속도가 지금은 비정상적이게 느립니다. (2017년 8월 기준)
* [ImageMagick](https://www.imagemagick.org/script/index.php)은 이미지 최적화용도로 자주 추천되는 툴입니다. 괜찮은 도구라 생각하나, 결과물이 나오기까지 거쳐야 하는 최적화 과정이 더 길며, 다른 툴을 사용하면 이것보다 더 좋은 결과물을 받아 볼 수 있습니다. 이 툴 대신 [libvips](https://github.com/jcupitt/libvips)를 사용해 보시는 것을 추천합니다. 그러나 더 저수준의 툴이기 때문에 사용하시려면 기술적인 지식을 좀 더 필요로 합니다. ImageMagick은 또한 [보안상 취약점](https://imagetragick.com/#moreinfo)이 발견된 역사를 가지고 있는데, 여러분이 왠지 알고 싶어할 것 같아 적어 놓습니다.
* 블링크(크롬에서 사용하는 렌더링 엔진)은 메인 스레드 밖에서 이미지를 디코딩합니다. 디코딩 관련 작업을 컴포지터(compositor) 스레드에서 하도록 옮기면 메인 스레드가 다른 일을 할 수 있는 여유가 생깁니다. 구글에서는 이를 연기시킨 디코딩(deffered decoding)이라고 부릅니다. 디코딩 작업이 뒤로 미뤄졌기 때문에, 프레임을 화면에 표시해야 하므로 크리티컬 패스 내에 디코딩 작업이 남아 있게 됩니다. 이 때문에 애니메이션이 버벅이는 현상(jank)이 발생할 수 있습니다. [`img.decode()`](https://html.spec.whatwg.org/multipage/embedded-content.html#dom-img-decode) API를 사용하면 이 버벅임 문제를 해결하는 데 도움이 됩니다.

<p class="license">The content of this book is licensed under the  Creative Commons [Attribution-NonCommercial-NoDerivs 2.0 Generic (CC BY-NC-ND 2.0)](https://creativecommons.org/licenses/by-nc-nd/2.0/) license, and code samples are licensed under the [Apache 2.0 License](http://www.apache.org/licenses/LICENSE-2.0). Copyright Google, 2017.</p>

</body>
</html>
