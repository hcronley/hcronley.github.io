---
layout: default
title: Flash — Study App
description: A Django and React Native flashcard app with LLM-backed card generation, document processing, and SM-2 spaced repetition.
---

# Flash — Study App

<p class="meta">
  <strong>Stack:</strong> <code>Django 6</code> · <code>Django REST Framework</code> ·
  <code>Expo / React Native</code> · <code>Redux Toolkit</code> · <code>Gemini</code> ·
  <code>SQLite</code>
</p>

## The Problem

Spaced repetition works, and almost nobody uses it, because making the cards is the
boring part. The material already exists — a lecture PDF, a slide deck, a set of notes —
but turning it into a deck is an hour of transcription before any studying happens. Flash
collapses that: upload the material, get a deck.

## The Pipeline

Upload → process by file type → extract content → apply preferences → generate cards →
schedule reviews. The whole chain runs automatically on upload; there's no manual step
between dropping in a PDF and having cards to study.

**Document processing** dispatches on file extension through a processor map —
`pdfplumber` for PDFs, `python-pptx` for slide decks, `python-docx` for Word, plain read
for text and Markdown. Type detection is automatic; the user never picks from a dropdown,
because asking someone to classify their own file is a question the computer should
answer.

**Generation is LLM-first with a rule-based fallback.** Extracted text is chunked, sent
to Gemini with a system prompt assembled from the user's preferences, and the returned
JSON is parsed, validated, merged, and capped. If the model errors out or returns
nothing, generation silently falls back to the rule-based path rather than failing the
request. The user gets cards either way — a worse deck beats an error message.

The generation prompt is genuinely parameterized: card count, difficulty, style
(concise / detailed / ELI5 / exam), focus topics, exclude topics, and output language
across eight languages. Preferences store the defaults; every setting is overridable per
generation. Card count distributes evenly across chunks so a long document doesn't
produce fifty cards about its first three pages.

**Scheduling uses SM-2** — ease-factor adjustment and interval calculation per
user-and-card, with three study modes (learn, review, cram) drawing from different card
pools.

## Architecture

The backend is a single Django app holding all domain logic across six models — Material,
ExtractedContent, Deck, Flashcard, UserPreferences, Review — exposed both as
template-rendered views and as a REST API. DRF with token auth covers seventeen routes:
auth, materials, decks, cards, preferences, generation, study, and export.

The mobile client is Expo / React Native with expo-router file-based routing, Redux
Toolkit for state across four slices, and a typed fetch wrapper so the API surface is
declared once rather than restated at every call site. The backend runs on EC2; the
client talks to it over the REST API.

Export was written in pure Python with no new dependencies — CSV, JSON, and Anki
tab-separated format, each optionally including review progress. Anyone who outgrows
Flash can leave with their decks intact.

## Notable Decisions

- **Deterministic fallback under the LLM.** Generation never hard-fails on a model error.
- **Auto-detection over configuration.** File type is inferred; the upload form asks for
  a file and nothing else.
- **Preferences as defaults, not constraints.** Saved settings pre-fill the generation
  form; nothing is locked.
- **Destructive actions are POST-only** with ownership verified through queryset
  filtering rather than a permission check bolted on afterward.

<div class="note">
  <p>
    Archived personal project. <!-- TODO: link the repo if you publish it, or delete this note. -->
  </p>
</div>
