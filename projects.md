---
layout: page
title: Projects
---
<header class="projects-hero">
    <div class="page-hero">
        <span class="kicker">Portfolio</span>
        <h1 class="page-title">Projects<span class="dot">.</span></h1>
        <p class="page-sub">AR experiments, open-source SDK work, and retail apps shipped to millions of users.</p>
        <p class="hero-stats"><span>3</span> AR demos <i>·</i> <span>1</span> SDK <i>·</i> <span>4</span> shipped apps</p>
    </div>
    <div class="projects-side">
        <div class="terminal vp-window" data-mode="select">
            <div class="terminal-bar" aria-hidden="true">
                <span class="tl-group"><span class="tl tl-r"></span><span class="tl tl-y"></span><span class="tl tl-g"></span></span>
                <span class="terminal-title">drone.usdz — Reality Composer</span>
            </div>
            <div class="vp-body">
                <div class="vp-tools" role="toolbar" aria-label="Viewport tools">
                    <button type="button" class="active" data-tool="select" aria-pressed="true" aria-label="Select" title="Select">&#x2B1A;</button>
                    <button type="button" data-tool="move" aria-pressed="false" aria-label="Move" title="Move">&#x2725;</button>
                    <button type="button" data-tool="rotate" aria-pressed="false" aria-label="Rotate" title="Rotate">&#x27F2;</button>
                    <button type="button" data-tool="scale" aria-pressed="false" aria-label="Scale" title="Scale">&#x2922;</button>
                </div>
                <div class="vp-scene" aria-hidden="true">
                    <div class="vp-orbit">
                        <div class="vp-floor">
                            <div class="vp-shadow"></div>
                            <div class="vp-path"></div>
                            <div class="vp-waypoint"></div>
                            <div class="vp-focus"><span></span><span></span><span></span><span></span></div>
                        </div>
                        <div class="vp-drone">
                            <div class="vp-dcore">
                                <div class="vp-dbody">
                                    <i class="f-front"></i><i class="f-back"></i>
                                    <i class="f-left"></i><i class="f-right"></i>
                                    <i class="f-top"></i>
                                </div>
                                <i class="vp-arm va1"></i><i class="vp-arm va2"></i>
                                <b class="vp-rotor vr1"><u></u></b>
                                <b class="vp-rotor vr2"><u></u></b>
                                <b class="vp-rotor vr3"><u></u></b>
                                <b class="vp-rotor vr4"><u></u></b>
                                <div class="vp-bounds">
                                    <i class="b-front"></i><i class="b-back"></i>
                                    <i class="b-left"></i><i class="b-right"></i>
                                </div>
                            </div>
                            <div class="vp-gz vp-gz-move"><i class="gx"></i><i class="gy"></i><i class="gz"></i></div>
                            <div class="vp-gz vp-gz-rot"><i class="rx"></i><i class="ry"></i><i class="rz"></i></div>
                        </div>
                        <div class="vp-person">
                            <div class="vp-pcore">
                                <div class="vp-pp pp-head"><i class="pf"></i><i class="pb"></i><i class="pl"></i><i class="pr"></i></div>
                                <div class="vp-pp pp-torso"><i class="pf"></i><i class="pb"></i><i class="pl"></i><i class="pr"></i></div>
                                <div class="vp-pp pp-arml"><i class="pf"></i><i class="pb"></i><i class="pl"></i><i class="pr"></i></div>
                                <div class="vp-pp pp-armr"><i class="pf"></i><i class="pb"></i><i class="pl"></i><i class="pr"></i></div>
                                <div class="vp-pp pp-legl"><i class="pf"></i><i class="pb"></i><i class="pl"></i><i class="pr"></i></div>
                                <div class="vp-pp pp-legr"><i class="pf"></i><i class="pb"></i><i class="pl"></i><i class="pr"></i></div>
                            </div>
                        </div>
                    </div>
                </div>
                <span class="vp-dust d1" aria-hidden="true"></span><span class="vp-dust d2" aria-hidden="true"></span>
                <span class="vp-dust d3" aria-hidden="true"></span><span class="vp-dust d4" aria-hidden="true"></span>
                <span class="vp-dust d5" aria-hidden="true"></span><span class="vp-dust d6" aria-hidden="true"></span>
                <div class="hud-anchor vp-entity" aria-hidden="true"><span class="pin"></span>drone_entity<em data-readout>selected</em></div>
                <div class="hud-anchor vp-way" aria-hidden="true"><span class="pin is-pink"></span>waypoint_01<em>12 m</em></div>
                <div class="hud-anchor vp-user" aria-hidden="true"><span class="pin is-violet"></span>user_01<em>tracking</em></div>
                <div class="vp-gizmo" aria-hidden="true">
                    <span class="arm ax"></span><span class="arm ay"></span><span class="arm az"></span>
                    <b class="lx">X</b><b class="ly">Y</b><b class="lz">Z</b>
                </div>
                <div class="hud-row hud-bottom" aria-hidden="true">
                    <span data-status>12,480 VERTS · 8 MATERIALS</span>
                    <span>REALITYKIT · 60 FPS</span>
                </div>
            </div>
        </div>
        <script>
        /* Viewport tools: each button switches the editor mode (CSS keys off
           data-mode on the window) and a light rAF loop prints a live readout
           of the transform being edited. Block comments only: the compress
           layout joins lines. */
        (function () {
            var win = document.querySelector('.vp-window');
            if (!win) { return; }
            var buttons = Array.prototype.slice.call(win.querySelectorAll('[data-tool]'));
            var readout = win.querySelector('[data-readout]');
            var status = win.querySelector('[data-status]');
            var drone = win.querySelector('.vp-drone');
            var core = win.querySelector('.vp-dcore');
            var STATUS = {
                select: '12,480 VERTS \u00b7 8 MATERIALS',
                move: 'TRANSLATE \u00b7 SNAP 0.1 M',
                rotate: 'ROTATE \u00b7 SNAP 15\u00b0',
                scale: 'SCALE \u00b7 UNIFORM'
            };
            var mode = 'select', raf = null;

            function matrix(el) {
                var t = window.getComputedStyle(el).transform;
                var M = window.DOMMatrix || window.WebKitCSSMatrix;
                return new M(t && t !== 'none' ? t : undefined);
            }
            function num(v, d) { var s = v.toFixed(d); return (v < 0 ? '\u2212' + s.slice(1) : s); }

            function tick() {
                if (mode === 'move') {
                    var m = matrix(drone);
                    readout.textContent = 'x ' + num(m.m41 / 100, 2) + '  z ' + num(m.m43 / 100, 2) + ' m';
                } else if (mode === 'rotate') {
                    var r = matrix(core);
                    var yaw = Math.atan2(-r.m13, r.m11) * 180 / Math.PI;
                    readout.textContent = 'yaw ' + Math.round((yaw + 360) % 360) + '\u00b0';
                } else if (mode === 'scale') {
                    var c = matrix(core);
                    readout.textContent = num(Math.sqrt(c.m11 * c.m11 + c.m12 * c.m12 + c.m13 * c.m13), 2) + '\u00d7';
                }
                raf = mode === 'select' ? null : window.requestAnimationFrame(tick);
            }

            function setMode(next) {
                mode = next;
                win.setAttribute('data-mode', next);
                buttons.forEach(function (b) {
                    var on = b.getAttribute('data-tool') === next;
                    b.classList.toggle('active', on);
                    b.setAttribute('aria-pressed', on ? 'true' : 'false');
                });
                status.textContent = STATUS[next];
                if (next === 'select') { readout.textContent = 'selected'; }
                if (next !== 'select' && !raf) { raf = window.requestAnimationFrame(tick); }
            }

            buttons.forEach(function (b) {
                b.addEventListener('click', function () { setMode(b.getAttribute('data-tool')); });
            });
        })();
        </script>
    </div>
</header>
<section class="about-section reveal">
    <div class="section-heading">
        <div>
            <span class="kicker">01 — Showcase</span>
            <h2>Built for fun</h2>
        </div>
        <span class="rule"></span>
    </div>

    {% include project-carousel.html %}
</section>

<section class="about-section reveal">
    <div class="section-heading">
        <div>
            <span class="kicker">02 — Open source</span>
            <h2>In the wild</h2>
        </div>
        <span class="rule"></span>
        <a class="more" href="https://github.com/{{ site.github }}" target="_blank" rel="noopener">More on GitHub &rarr;</a>
    </div>

    <a class="repo-card" href="https://github.com/Esri/arcgis-maps-sdk-swift-samples" target="_blank" rel="noopener">
        <div class="repo-info">
            <h3>Esri / arcgis-maps-sdk-swift-samples</h3>
            <p>The official sample collection for the ArcGIS Maps SDK for Swift — the code enterprise and indie developers learn the SDK from. I help maintain it as a Product Engineer at Esri.</p>
            <p class="repo-tags">Swift · SwiftUI · ArcGIS</p>
        </div>
        <span class="card-arrow">&#8599;</span>
    </a>
</section>

<section class="about-section reveal">
    <div class="section-heading">
        <div>
            <span class="kicker">03 — Shipped</span>
            <h2>Professional work</h2>
        </div>
        <span class="rule"></span>
    </div>

    <div class="ship-grid">
        <div class="ship-card">
            <span class="ship-org">PredictSpring &rarr; Salesforce</span>
            <h3>SuitSupply Point of Sale</h3>
            <p>iPadOS point-of-sale for SuitSupply stores, with Bluetooth and IP hardware integration for faster in-store checkout.</p>
            <p class="ship-meta">iPadOS · Retail</p>
        </div>
        <div class="ship-card">
            <span class="ship-org">PredictSpring &rarr; Salesforce</span>
            <h3>Toys&nbsp;"R"&nbsp;Us Point of Sale</h3>
            <p>iPadOS point-of-sale deployment on a commerce platform serving 500K+ daily active users for Fortune 500 retailers.</p>
            <p class="ship-meta">iPadOS · Retail</p>
        </div>
        <div class="ship-card">
            <span class="ship-org">DYNAMIT &rarr; TELUS</span>
            <h3>Donatos Pizza</h3>
            <p>Consumer ordering app for a beloved pizza chain — modular, testable iOS architecture with full CI/CD pipelines.</p>
            <p class="ship-meta">iOS · 100K+ downloads</p>
        </div>
        <div class="ship-card">
            <span class="ship-org">DYNAMIT &rarr; TELUS</span>
            <h3>Allergan Brilliant Distinctions</h3>
            <p>Consumer rewards app (since rebranded to All&#275;) shipped to the App Store with crash diagnostics wired through Fabric.</p>
            <p class="ship-meta">iOS · Consumer</p>
        </div>
    </div>
</section>
