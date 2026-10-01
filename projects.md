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
    <div class="projects-side" aria-hidden="true">
        <div class="terminal vp-window">
            <div class="terminal-bar">
                <span class="tl tl-r"></span><span class="tl tl-y"></span><span class="tl tl-g"></span>
                <span class="terminal-title">drone.usdz — Reality Composer</span>
            </div>
            <div class="vp-body">
                <div class="vp-tools"><span class="active">&#x2B1A;</span><span>&#x2725;</span><span>&#x27F2;</span><span>&#x2922;</span></div>
                <div class="vp-scene">
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
                <span class="vp-dust d1"></span><span class="vp-dust d2"></span>
                <span class="vp-dust d3"></span><span class="vp-dust d4"></span>
                <span class="vp-dust d5"></span><span class="vp-dust d6"></span>
                <div class="hud-anchor vp-entity"><span class="pin"></span>drone_entity<em>selected</em></div>
                <div class="hud-anchor vp-way"><span class="pin is-pink"></span>waypoint_01<em>12 m</em></div>
                <div class="hud-anchor vp-user"><span class="pin is-violet"></span>user_01<em>tracking</em></div>
                <div class="vp-gizmo">
                    <span class="arm ax"></span><span class="arm ay"></span><span class="arm az"></span>
                    <b class="lx">X</b><b class="ly">Y</b><b class="lz">Z</b>
                </div>
                <div class="hud-row hud-bottom">
                    <span>12,480 VERTS · 8 MATERIALS</span>
                    <span>REALITYKIT · 60 FPS</span>
                </div>
            </div>
        </div>
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
