---
title: About
layout: page
---
<header class="about-hero">
    <div class="about-hero-copy">
        <span class="kicker">Hello</span>
        <h1 class="page-title">Hi, I'm Chris<span class="dot">.</span></h1>
        <p class="page-sub">Product engineer, Columbia CS grad, and serial side-project starter.</p>
        <p class="lede">I'm a product engineer with 8 years shipping iOS apps and SDKs — now building AR and VPS capabilities at Esri.</p>
        <p class="hero-p">I've led technical decisions for Fortune 500 commerce platforms, contributed to two successful startup acquisitions, and shipped consumer apps with hundreds of thousands of daily users. Most of my work is in Swift, and I'm just as comfortable in Python, JavaScript, C++, and Java.</p>
        <p class="hero-p">These days my work lives where software meets the physical world — at Esri I prototype new features, build Visual Positioning System capabilities for AR, and manage releases of the ArcGIS Maps SDK for Swift.</p>
    </div>
    <div class="about-side">
        <aside class="id-card">
            <div class="id-top">
                <span class="id-brand">cw<span class="dot">.</span></span>
                <span class="id-num">ID-2024-ESRI</span>
            </div>
            <img class="id-avatar" src="/{{ site.picture }}" alt="{{ site.title }}">
            <h3 class="id-name">Chris Webb</h3>
            <p class="id-role">Product Engineer</p>
            <ul class="id-rows">
                <li><span class="k">employer</span><span class="v">Esri</span></li>
                <li><span class="k">education</span><span class="v">Columbia CS '25</span></li>
                <li><span class="k">focus</span><span class="v">Swift · AR · GIS</span></li>
                <li><span class="k">status</span><span class="v is-live"><i></i>building</span></li>
            </ul>
            <div class="id-foot">
                <span class="id-barcode" aria-hidden="true"></span>
                <span class="id-est">est. 2017</span>
            </div>
            <span class="id-holo" aria-hidden="true"></span>
            <span class="id-glare" aria-hidden="true"></span>
        </aside>
        <script>
        /* Holo foil, glare and tilt for the ID card, modeled on
           simeydotme/pokemon-cards-css. Touch or hover sets spring targets
           from the pointer position; springs (same constants as the original)
           follow it, and half a second after release they ease back with a
           soft, slightly wobbly settle. Block comments only: the compress
           layout joins lines. */
        (function () {
            var card = document.querySelector('.id-card');
            if (!card) { return; }
            if (window.matchMedia && window.matchMedia('(prefers-reduced-motion: reduce)').matches) { return; }

            var FOLLOW = { k: 0.066, c: 0.25 };
            var SETTLE = { k: 0.01, c: 0.06 };
            function spring(v) { return { x: v, v: 0, t: v }; }
            var s = { mx: spring(50), my: spring(50), bx: spring(50), by: spring(50), rx: spring(0), ry: spring(0), o: spring(0) };
            var mode = FOLLOW, raf = null, last = 0, endTimer = null;

            function clamp(v, lo, hi) { return Math.max(lo, Math.min(hi, v)); }
            function adjust(v, a, b, c, d) { return c + (d - c) * (v - a) / (b - a); }

            function render() {
                var st = card.style;
                st.setProperty('--mx', s.mx.x.toFixed(2) + '%');
                st.setProperty('--my', s.my.x.toFixed(2) + '%');
                st.setProperty('--bx', s.bx.x.toFixed(2) + '%');
                st.setProperty('--by', s.by.x.toFixed(2) + '%');
                st.setProperty('--o', clamp(s.o.x, 0, 1).toFixed(3));
                var ax = s.ry.x, ay = s.rx.x, mag = Math.sqrt(ax * ax + ay * ay);
                st.setProperty('--tilt', mag < 0.01 ? '0 0 1 0deg' : (ax / mag).toFixed(4) + ' ' + (ay / mag).toFixed(4) + ' 0 ' + mag.toFixed(2) + 'deg');
            }

            function step(now) {
                var dt = last ? Math.min((now - last) / (1000 / 60), 3) : 1;
                last = now;
                var moving = false;
                Object.keys(s).forEach(function (key) {
                    var p = s[key];
                    var a = mode.k * (p.t - p.x) - mode.c * p.v;
                    p.v += a * dt;
                    p.x += p.v * dt;
                    /* opacity must not bounce back above 0 once faded: a
                       flicker. Tilt and glare keep their soft wobble. */
                    if (key === 'o' && p.t === 0 && p.x <= 0) { p.x = 0; p.v = 0; }
                    if (Math.abs(p.t - p.x) > 0.01 || Math.abs(p.v) > 0.01) { moving = true; } else { p.x = p.t; p.v = 0; }
                });
                render();
                raf = moving ? requestAnimationFrame(step) : null;
                if (!raf) { last = 0; }
            }
            function kick() { if (!raf) { raf = requestAnimationFrame(step); } }

            function interact(e) {
                clearTimeout(endTimer);
                mode = FOLLOW;
                var r = card.getBoundingClientRect();
                var px = clamp((e.clientX - r.left) / r.width * 100, 0, 100);
                var py = clamp((e.clientY - r.top) / r.height * 100, 0, 100);
                s.mx.t = px; s.my.t = py; s.o.t = 1;
                s.bx.t = adjust(px, 0, 100, 37, 63);
                s.by.t = adjust(py, 0, 100, 33, 67);
                s.rx.t = -(px - 50) / 3.5;
                s.ry.t = (py - 50) / 3.5;
                kick();
            }
            function end(delay) {
                clearTimeout(endTimer);
                endTimer = setTimeout(function () {
                    mode = SETTLE;
                    s.mx.t = 50; s.my.t = 50; s.bx.t = 50; s.by.t = 50;
                    s.rx.t = 0; s.ry.t = 0; s.o.t = 0;
                    kick();
                }, delay);
            }

            card.addEventListener('pointerdown', interact);
            card.addEventListener('pointermove', interact);
            card.addEventListener('pointerup', function (e) { if (e.pointerType !== 'mouse') { end(500); } });
            card.addEventListener('pointercancel', function () { end(0); });
            card.addEventListener('pointerleave', function () { end(100); });

            /* Click/tap: a quick spin on the vertical axis. composite: 'add'
               layers the rotateY on top of the float animation's transform
               (and the tilt uses the separate rotate property), so all three
               combine. A click mid-spin is ignored rather than restarting.
               The curve is near-symmetric: the last 5 degrees take ~80ms, so
               the spin lands without a slow crawl at the end. */
            var spinning = null;
            card.addEventListener('click', function () {
                if (spinning || !card.animate) { return; }
                try {
                    spinning = card.animate(
                        [{ transform: 'rotateY(0deg)' }, { transform: 'rotateY(360deg)' }],
                        { duration: 600, easing: 'cubic-bezier(0.5, 0, 0.3, 1)', composite: 'add' }
                    );
                    spinning.onfinish = spinning.oncancel = function () { spinning = null; };
                } catch (err) { spinning = null; }
            });
        })();
        </script>
    </div>
</header>

<section class="about-section reveal">
    <div class="section-heading is-centered">
        <div>
            <span class="kicker">01 — Currently</span>
            <h2>State of the union</h2>
        </div>
    </div>

    <ul class="about-facts">
        <li>
            <span class="k">based in</span>
            <span class="v">Redlands, California</span>
        </li>
        <li>
            <span class="k">role</span>
            <span class="v">Product Engineer at Esri — ArcGIS Maps SDK for Swift</span>
        </li>
        <li>
            <span class="k">education</span>
            <span class="v">B.A. Computer Science, Columbia University (2025)</span>
        </li>
        <li>
            <span class="k">ask me about</span>
            <span class="v">Swift, ARKit &amp; spatial computing</span>
        </li>
        <li>
            <span class="k">contact</span>
            <span class="v"><a href="https://www.linkedin.com/in/{{ site.linkedin }}" target="_blank" rel="noopener">LinkedIn</a> &nbsp;·&nbsp; <a href="mailto:{{ site.email }}">{{ site.email }}</a></span>
        </li>
    </ul>
</section>

<section class="about-section reveal">
    <div class="section-heading is-centered">
        <div>
            <span class="kicker">02 — Path</span>
            <h2>How I got here</h2>
        </div>
    </div>

    <ol class="timeline">
        <li class="is-now">
            <span class="tl-label">Now</span>
            <div class="tl-body">
                <h3>Esri — Product Engineer</h3>
                <p>Joined as an intern in 2024, full-time since 2025 — prototyping SDK features, building Visual Positioning System capabilities for AR, and managing releases of the ArcGIS Maps SDK for Swift.</p>
            </div>
        </li>
        <li>
            <span class="tl-label">2021</span>
            <div class="tl-body">
                <h3>Columbia University</h3>
                <p>Went back to school mid-career for a B.A. in computer science (2021–2025), freelancing iOS work for startups and small businesses in New York along the way.</p>
            </div>
        </li>
        <li>
            <span class="tl-label">2019</span>
            <div class="tl-body">
                <h3>PredictSpring — Senior Software Engineer</h3>
                <p>Engineered a mobile commerce platform serving 500K+ daily active users for Fortune 500 retailers, and integrated in-store POS hardware. PredictSpring was later acquired by Salesforce.</p>
            </div>
        </li>
        <li>
            <span class="tl-label">2018</span>
            <div class="tl-body">
                <h3>DYNAMIT — iOS Engineer</h3>
                <p>Built modular, testable iOS apps and CI/CD pipelines; shipped consumer apps with 100K+ downloads. DYNAMIT was acquired by TELUS / WillowTree.</p>
            </div>
        </li>
        <li>
            <span class="tl-label">2017</span>
            <div class="tl-body">
                <h3>Down the AR rabbit hole</h3>
                <p>Started experimenting with ARKit and CoreLocation, writing the blog series and demos that became this site's projects.</p>
            </div>
        </li>
    </ol>
</section>

<section class="about-section reveal">
    <div class="section-heading is-centered">
        <div>
            <span class="kicker">03 — Toolbox</span>
            <h2>What I work with</h2>
        </div>
    </div>

    <div class="stack-groups">
        <div class="stack-row">
            <span class="k">languages</span>
            <ul class="skill-chips">
                <li>Swift</li>
                <li>Python</li>
                <li>JavaScript</li>
                <li>C++</li>
                <li>Java</li>
            </ul>
        </div>
        <div class="stack-row">
            <span class="k">platforms</span>
            <ul class="skill-chips">
                <li>UIKit</li>
                <li>ARKit &amp; SceneKit</li>
                <li>ArcGIS Maps SDK</li>
                <li>Flask</li>
                <li>React</li>
            </ul>
        </div>
        <div class="stack-row">
            <span class="k">interests</span>
            <ul class="skill-chips">
                <li>Spatial Computing</li>
                <li>SDK Development</li>
                <li>Machine Learning</li>
                <li>LLM Tooling</li>
            </ul>
        </div>
    </div>
</section>
