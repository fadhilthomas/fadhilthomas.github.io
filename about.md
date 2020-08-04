---
title: Publicity
layout: page
---
<header class="header-home {% if site.animation %}animated{% endif %}">
        {% if site.about %}
            <a class="link" href="{{ site.url }}/about">
                <img class="selfie" alt="{{ site.name }}" src="{% if site.external-image %}{{ site.picture }}{% else %}{{ site.url }}/{{ site.picture }}{% endif %}" />
            </a>
        {% else %}
            <span class="link">
                <img class="selfie" alt="{{ site.name }}" src="{% if site.external-image %}{{ site.picture }}{% else %}{{ site.url }}/{{ site.picture }}{% endif %}" />
            </span>
        {% endif %}

        <h1 class="title">{{ site.name }}</h1>
        <h2 class="description">{{ site.bio }}</h2>
		{% if page.title == "Home" %}
		<h1>
                <span id="typed"></span>
                <div id="typed-strings">
                    <p><span class="highlight pink">security engineer</span></p>
                    <p><span class="highlight azure">android engineer</span></p>
                </div>
            </h1>
		{% endif %}
        {% include social-links.html %}
</header>
<hr>
<section class="list">
<div class="item star">
    <a class="url" href="https://cyberthreat.id/read/7481">
	<h3 class="title">[Cyberthreat.id] - Cerita Muhammad Thomas Fadhila Temukan Bug di Tokopedia dan Instagram</h3>
    </a>
</div>
<br/><br/>
<div class="item star">
    <a class="url" href="https://lampung.rilis.id/Thomas-Fadhila-Alumnus-Teknokrat-yang-Sukses-Temukan-Bug-di-Tokopedia-dan-Instagram.html">
	<h3 class="title">[Rilis.id] - Thomas Fadhila, Alumnus Teknokrat yang Sukses Temukan “Bug” di Tokopedia dan Instagram</h3>
    </a>
</div>
<br/><br/>
<div class="item star">
    <a class="url" href="https://lampungpro.co/post/29030/penemu-bug-di-aplikasi-tokopedia-dan-instagram-ternyata-alumni-universitas-teknokrat-indonesia">
	<h3 class="title">[LampungPro] - Penemu 'Bug' di Aplikasi Tokopedia dan Instagram Ternyata Alumni Universitas Teknokrat Indonesia</h3>
    </a>
</div>
<br/><br/>
<div class="item star">
    <a class="url" href="https://radarlampung.co.id/2020/07/10/kisah-alumni-teknokrat-temukan-bug-instagram-dan-tokopedia/">
	<h3 class="title">[Radar Lampung] - Kisah Alumni Teknokrat Temukan Bug Instagram dan Tokopedia</h3>
    </a>
</div>
<br/><br/>
<div class="item star">
    <a class="url" href="https://warta9.com/thomas-fadhila-alumni-universitas-teknokrat-penemu-bug-di-tokopedia-dan-instagram/">
	<h3 class="title">[Warta9] - Thomas Fadhila, Alumni Universitas Teknokrat Penemu Bug di Tokopedia dan Instagram</h3>
    </a>
</div>
<br/><br/>
<div class="item star">
    <a class="url" href="https://lampung.tribunnews.com/2020/07/29/cerita-pemudalampung-temukanbug-instagram-dan-tokopedia-thomas-dapat-imbalan750-dolar">
	<h3 class="title">[Tribun News] - Cerita Pemuda Lampung Temukan Bug Instagram dan Tokopedia, Thomas Dapat Imbalan 750 Dolar</h3>
    </a>
</div>
</section>