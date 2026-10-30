#!/usr/bin/env python3
"""Aligns portfolio index.html with the resume and adds Rashfa Cafe with a live preview.
Usage: python3 patch_portfolio.py [path/to/index.html]   (a .bak copy is saved first)
"""
import re, shutil, sys

# >>> Set this to the real deployed URL of the Rashfa Cafe menu <<<
RASHFA_LIVE = "https://hamarigo.github.io/rashfa-cafe-restaurent-menu/"
RASHFA_REPO = "https://github.com/HamaRigo/rashfa-cafe-restaurent-menu"

path = sys.argv[1] if len(sys.argv) > 1 else "index.html"
shutil.copy(path, path + ".bak")
h = open(path, encoding="utf-8").read()

def sub(pat, rep, n=0):
    global h
    h, k = re.subn(pat, lambda m: rep, h, count=n, flags=re.S)
    if not k:
        print("WARNING no match:", pat[:70])

def rep(old, new):
    sub(re.escape(old), new)

# Hero
sub(r'Software Engineer — <span>.*?</p>',
    'Software Engineer — <span>Backend, Microservices &amp; Distributed Systems</span>, building secure, event-driven platforms for fintech and marketplace use cases.</p>', 1)
rep('Full-Stack · Microservices · AI/RAG', 'Full-Stack · Microservices · Event-Driven')
rep('<div class="stat-val">15+</div>', '<div class="stat-val">12</div>')

# Marquee + skills
rep('<span class="marquee-item">OpenAI / Claude RAG</span>', '')
sub(r'<div class="skills-grid">.*?\n  </div>\n  <div class="tech-cloud', '<div class="tech-cloud')
sub(r'<span class="tech-chip">(LLM Agents / Chatbots|RAG Pipelines|Cursor \(AI Dev\))</span>\s*', '')
rep('<span class="tech-chip">Stripe Payments</span>', '<span class="tech-chip">Stripe Payments</span><span class="tech-chip">Tesseract OCR</span>')
rep('<span class="tech-chip">PCI-DSS</span>', '<span class="tech-chip">PCI-DSS (working knowledge)</span>')

# Experience: Rigoo
rep('Full Stack Engineering Consultant, Backend &amp; Distributed Systems', 'Full Stack Engineering Consultant — Rigoo Marine')
rep('Aug 2025 — Present', 'Aug 2025 — Mar 2026')
rep('Independent Consultant, Fintech &amp; Enterprise Clients · Doha, Qatar', 'Independent Consultant · Marine services, Doha, Qatar')
sub(r'<li>Sole engineer on Rigoo Marine.*?</ul>', """<li>Sole engineer on Rigoo Marine, a live bilingual (Arabic/English, full RTL) marine-services platform with online booking for the Qatar market — owned the architecture, a 12-service Spring Cloud backend, the React frontend, and Dockerized deployment end-to-end</li>
          <li>Secured services with per-service JWT and Redis-backed gateway rate limiting</li>
          <li>Instrumented Prometheus metrics and distributed tracing for rapid problem detection and diagnosis</li>
          <li>Applied circuit breaker, retry, and bulkhead patterns so failing dependencies degrade gracefully</li>
          <li>Integrated Stripe Checkout with idempotent webhook ingestion across the parts shop and invoicing</li>
        </ul>""", 1)
rep('<span class="tl-tag tag-blue">PCI-DSS</span>', '<span class="tl-tag tag-blue">Prometheus &amp; Tracing</span>')

# Experience: ATB
sub(r'<li>Rolled out a Keycloak.*?</ul>', """<li>Built a Spring Cloud platform of 9 modules (Gateway, Eureka, Config Server, five business services, shared library) communicating through asynchronous Kafka events</li>
          <li>Secured the gateway and every service with Keycloak SSO and role-based access (OIDC/JWT)</li>
          <li>Built a document pipeline for multilingual Tesseract OCR (French/Arabic/English) of cheques, bills and IDs, plus a fraud-scoring service flagging a 0–1 risk score at 0.7</li>
          <li>Deployed an 11-service Docker Compose stack with Prometheus, Grafana, Fluentd/Elasticsearch logging and Zipkin tracing</li>
        </ul>""", 1)
rep('<span class="tl-tag tag-green">Kafka Microservices</span>', '<span class="tl-tag tag-green">Kafka Events</span>')
rep('<span class="tl-tag tag-pink">OCR &amp; ML</span>', '<span class="tl-tag tag-pink">Tesseract OCR</span>')
rep('<span class="tl-tag tag-amber">10K+ Users</span>', '<span class="tl-tag tag-amber">11-Service Stack</span>')

# Experience: telecom
rep('Network eXpertise / Orange LTE, Alliance et Performance', 'Network eXpertise / Orange LTE, FIRSTCOM, Alliance et Performance')

# Projects: Grocery
rep(', structured as a modular monolith that can be split into standalone services per module without a rewrite', ', structured as a modular monolith')
sub(r'<li>Built an LLM-powered agent.*?</li>', '<li>Designed the retrieval layer (Elasticsearch, kept in sync by the event stream) as the foundation for a planned LLM agent with RAG</li>', 1)
rep('<span class="proj-tag">LLM / RAG</span>', '')

# Projects: Rigoo
rep('Mar — Jun 2026', 'Aug 2025 — Mar 2026')
rep('Spring Cloud Gateway (Eureka) → 10 Microservices', 'Spring Cloud Gateway (Eureka) → 12 Microservices')
rep('10-service', '12-service')
rep('three role-based dashboards', 'five role-based dashboards (client, technician, team lead, delivery, admin)')
sub(r'\s*<li>Integrated an LLM-powered chatbot.*?</li>', '', 1)
rep('<a href="https://github.com/HamaRigo/rigoo-marine"', '<a href="https://rigoomarine.com" target="_blank" class="proj-btn proj-btn-vercel">rigoomarine.com ↗</a>\n        <a href="https://github.com/HamaRigo/rigoo-marine"')

# Projects: replace fraud card with ATB card + add Rashfa Cafe
new_cards = f"""<div class="proj-card reveal" style="transition-delay:.2s">
      <div class="proj-emoji">🔍</div>
      <div class="proj-name">OCR &amp; Fraud Detection Platform (ATB)</div>
      <a href="https://github.com/HamaRigo/realtime-fraud-pipeline" target="_blank" class="proj-link">github.com/HamaRigo/realtime-fraud-pipeline ↗</a>
      <span class="proj-link" style="color:var(--muted);">Sep 2024 — Feb 2025 · End-of-studies project</span>
      <div class="proj-arch">Upload → Tesseract OCR → Fraud Scorer (0–1) → Kafka: ocr-results / fraud-alerts</div>
      <ul class="tl-bullets">
        <li>Scored OCR-processed cheques, bills and IDs for fraud on a 0–1 scale, flagging results at 0.7 through Kafka topics ocr-results and fraud-alerts</li>
        <li>Secured every service with Keycloak SSO and kept the pipeline running through scoring-service outages with graceful degradation</li>
        <li>Observability via Prometheus, Grafana, Fluentd/Elasticsearch and Zipkin in an 11-service Docker Compose stack</li>
      </ul>
      <div class="proj-stack">
        <span class="proj-tag">Spring Cloud</span><span class="proj-tag">Kafka</span><span class="proj-tag">Keycloak</span><span class="proj-tag">Tesseract OCR</span>
      </div>
    </div>
    <div class="proj-card reveal" style="transition-delay:.25s">
      <div class="proj-emoji">☕</div>
      <div class="proj-name">Rashfa Cafe — Bilingual QR Menu</div>
      <span class="proj-link" style="color:var(--muted);">Apr 2026 · Doha, Qatar</span>
      <div class="proj-embed">
        <iframe src="{RASHFA_LIVE}" title="Rashfa Cafe — live preview" loading="lazy" referrerpolicy="no-referrer"></iframe>
      </div>
      <div class="proj-buttons">
        <a href="{RASHFA_LIVE}" target="_blank" class="proj-btn proj-btn-live">Live Preview ↗</a>
        <a href="{RASHFA_REPO}" target="_blank" class="proj-btn proj-btn-gh">GitHub ↗</a>
      </div>
      <ul class="tl-bullets">
        <li>Bilingual Arabic RTL / English single-page menu reached by QR code, with a language toggle</li>
        <li>Five categories priced in QAR rendered from structured data; story, gallery and visit sections with WhatsApp and Instagram links</li>
      </ul>
      <div class="proj-stack">
        <span class="proj-tag">HTML</span><span class="proj-tag">CSS</span><span class="proj-tag">Vanilla JS</span><span class="proj-tag">Arabic RTL</span>
      </div>
    </div>
    """
sub(r'<div class="proj-card reveal" style="transition-delay:\.2s">.*?(?=<div class="proj-card reveal" style="transition-delay:\.3s">)', new_cards, 1)

# Education
rep('Graduated 2025', 'Graduated 2025 · Night/part-time program, while employed full-time')

open(path, "w", encoding="utf-8").write(h)
print("Patched", path)
