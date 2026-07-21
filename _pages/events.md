---
title: "Upcoming Events"
permalink: "/events.html"
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
.event-date .day.is-range {
  font-size: 1.35rem;
}
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
.badge-private {
  display: inline-block;
  font-size: 0.78rem;
  font-weight: 700;
  color: #6534a0;
  background: #f6f3fa;
  border: 1px solid #e6ddf3;
  border-radius: 20px;
  padding: 0.3rem 0.8rem;
}
</style>

QCSP hosts workshops, hackathons, and community gatherings throughout the year. Here's what's coming up.

<div class="event-list">
{% assign today = "now" | date: "%s" %}
{% assign upcoming = "" | split: "" %}
{% for event in site.data.events %}
  {% assign event_ts = event.date | date: "%s" %}
  {% if event_ts >= today %}
    {% assign upcoming = upcoming | push: event %}
  {% endif %}
{% endfor %}
{% assign upcoming = upcoming | sort: "date" %}

{% for event in upcoming %}
<div class="event-card">
  <div class="event-date">
    {% if event.end_date %}
      {% assign same_month = event.date | date: "%Y-%m" %}
      {% assign end_month = event.end_date | date: "%Y-%m" %}
      {% if same_month == end_month %}
        <span class="month">{{ event.date | date: "%b" }}</span>
        <span class="day is-range">{{ event.date | date: "%-d" }}&ndash;{{ event.end_date | date: "%-d" }}</span>
        <span class="year">{{ event.date | date: "%Y" }}</span>
      {% else %}
        <span class="range-crossmonth">{{ event.date | date: "%b %-d" }}&ndash;{{ event.end_date | date: "%b %-d" }}</span>
        <span class="year">{{ event.date | date: "%Y" }}</span>
      {% endif %}
    {% else %}
      <span class="month">{{ event.date | date: "%b" }}</span>
      <span class="day">{{ event.date | date: "%d" }}</span>
      <span class="year">{{ event.date | date: "%Y" }}</span>
    {% endif %}
  </div>
  <div class="event-body">
    <h2 class="event-title">{{ event.title }}</h2>
    <div class="event-meta">
      {% if event.time %}<span>{{ event.time }}</span>{% endif %}
      {% if event.venue %}<span>{{ event.venue }}</span>{% endif %}
    </div>
    {% if event.description %}<p class="event-desc">{{ event.description }}</p>{% endif %}
    <div class="event-actions">
      {% if event.private %}
        <span class="badge-private">Invite-only</span>
      {% elsif event.register_url %}
        <a class="btn btn-dark btn-sm" href="{{ event.register_url }}">Register</a>
      {% endif %}
      {% if event.learn_more_url %}<a class="btn btn-outline-secondary btn-sm" href="{{ event.learn_more_url }}">Learn more</a>{% endif %}
    </div>
  </div>
</div>
{% else %}
<p class="text-muted">No upcoming events right now &mdash; check back soon, or see our <a href="{{site.baseurl}}/news.html">latest news</a> in the meantime.</p>
{% endfor %}
</div>
