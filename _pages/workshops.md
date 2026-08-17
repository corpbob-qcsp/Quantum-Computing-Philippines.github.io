---
title: "Workshops"
permalink: "/workshops.html"
---

<style>
.event-list { display: flex; flex-direction: column; gap: 1.1rem; margin-top: 1.5rem; }
.event-card {
  display: flex;
  background: #fff;
  border: 1px solid #ece8f2;
  border-radius: 10px;
  overflow: hidden;
}
.event-date {
  flex: 0 0 88px;
  background: #f6f3fa;
  border-right: 1px solid #ece8f2;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 1rem 0.5rem;
  text-align: center;
}
.event-date .month {
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.08em;
  text-transform: uppercase;
  color: #dc3545;
}
.event-date .day {
  font-size: 1.9rem;
  font-weight: 800;
  line-height: 1.1;
  color: #6534a0;
}
.event-date .day.is-range { font-size: 1.35rem; }
.event-date .range-crossmonth {
  font-size: 0.92rem;
  font-weight: 800;
  line-height: 1.3;
  color: #6534a0;
}
.event-date .year { font-size: 0.72rem; color: #888; }
.event-body { padding: 1rem 1.2rem; flex: 1; min-width: 0; }
.event-title { font-size: 1.08rem; font-weight: 700; margin: 0 0 0.3rem; }
.event-meta { font-size: 0.86rem; color: #888; margin: 0 0 0.6rem; display: flex; flex-wrap: wrap; gap: 0.2rem 0.9rem; }
.event-desc { font-size: 0.94rem; line-height: 1.55; margin: 0 0 0.9rem; }
.event-actions { display: flex; align-items: center; gap: 0.6rem; }
.event-card.is-past { opacity: 0.85; }
.event-card.is-past .event-date { background: #f5f5f5; }
.event-card.is-past .event-date .month,
.event-card.is-past .event-date .range-crossmonth { color: #888; }
.event-card.is-past .event-date .day { color: #555; }
.section-heading { margin-top: 2.5rem; margin-bottom: 0; }
</style>

QCSP's Workshop on Quantum Computing (Algorithms and Programming) is an annual hands-on event introducing participants to quantum algorithms and programming. Here's what's coming up, and what we've already held.

<div class="event-list">
{% assign today = "now" | date: "%s" %}
{% assign upcoming = "" | split: "" %}
{% assign past = "" | split: "" %}
{% for w in site.data.workshops %}
  {% assign w_ts = w.date | date: "%s" %}
  {% if w_ts >= today %}
    {% assign upcoming = upcoming | push: w %}
  {% else %}
    {% assign past = past | push: w %}
  {% endif %}
{% endfor %}
{% assign upcoming = upcoming | sort: "date" %}
{% assign past = past | sort: "date" | reverse %}

<h2 class="h4 section-heading">Upcoming</h2>

{% for w in upcoming %}
<div class="event-card">
  <div class="event-date">
    {% if w.end_date %}
      {% assign same_month = w.date | date: "%Y-%m" %}
      {% assign end_month = w.end_date | date: "%Y-%m" %}
      {% if same_month == end_month %}
        <span class="month">{{ w.date | date: "%b" }}</span>
        <span class="day is-range">{{ w.date | date: "%-d" }}&ndash;{{ w.end_date | date: "%-d" }}</span>
        <span class="year">{{ w.date | date: "%Y" }}</span>
      {% else %}
        <span class="range-crossmonth">{{ w.date | date: "%b %-d" }}&ndash;{{ w.end_date | date: "%b %-d" }}</span>
        <span class="year">{{ w.date | date: "%Y" }}</span>
      {% endif %}
    {% else %}
      <span class="month">{{ w.date | date: "%b" }}</span>
      <span class="day">{{ w.date | date: "%d" }}</span>
      <span class="year">{{ w.date | date: "%Y" }}</span>
    {% endif %}
  </div>
  <div class="event-body">
    <h2 class="event-title">{{ w.title }}</h2>
    <div class="event-meta">
      {% if w.venue %}<span>{{ w.venue }}</span>{% endif %}
    </div>
    {% if w.description %}<p class="event-desc">{{ w.description }}</p>{% endif %}
    {% if w.url %}
    <div class="event-actions">
      <a class="btn btn-dark btn-sm" href="{% unless w.url contains '://' %}{{site.baseurl}}{% endunless %}{{ w.url }}">View workshop</a>
    </div>
    {% endif %}
  </div>
</div>
{% else %}
<p class="text-muted">No upcoming workshops right now &mdash; check back soon, or see our <a href="{{site.baseurl}}/events.html">events page</a> in the meantime.</p>
{% endfor %}
</div>

{% if past.size > 0 %}
<h2 class="h4 section-heading">Past Workshops</h2>

<div class="event-list">
{% for w in past %}
<div class="event-card is-past">
  <div class="event-date">
    {% if w.end_date %}
      {% assign same_month = w.date | date: "%Y-%m" %}
      {% assign end_month = w.end_date | date: "%Y-%m" %}
      {% if same_month == end_month %}
        <span class="month">{{ w.date | date: "%b" }}</span>
        <span class="day is-range">{{ w.date | date: "%-d" }}&ndash;{{ w.end_date | date: "%-d" }}</span>
        <span class="year">{{ w.date | date: "%Y" }}</span>
      {% else %}
        <span class="range-crossmonth">{{ w.date | date: "%b %-d" }}&ndash;{{ w.end_date | date: "%b %-d" }}</span>
        <span class="year">{{ w.date | date: "%Y" }}</span>
      {% endif %}
    {% else %}
      <span class="month">{{ w.date | date: "%b" }}</span>
      <span class="day">{{ w.date | date: "%d" }}</span>
      <span class="year">{{ w.date | date: "%Y" }}</span>
    {% endif %}
  </div>
  <div class="event-body">
    <h2 class="event-title">{{ w.title }}</h2>
    <div class="event-meta">
      {% if w.venue %}<span>{{ w.venue }}</span>{% endif %}
    </div>
    {% if w.description %}<p class="event-desc">{{ w.description }}</p>{% endif %}
    {% if w.url %}
    <div class="event-actions">
      <a class="btn btn-outline-secondary btn-sm" href="{% unless w.url contains '://' %}{{site.baseurl}}{% endunless %}{{ w.url }}">View workshop</a>
    </div>
    {% endif %}
  </div>
</div>
{% endfor %}
</div>
{% endif %}
