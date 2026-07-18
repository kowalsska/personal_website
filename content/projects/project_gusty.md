---
title: "Gusty - booking platform for water sports schools"
date: 2025-04-01T00:00:00+00:00
draft: false
---

<img src="../../img/gusty_homepage.png">

<a href="https://gustyapp.com" target="_blank">🌐 gustyapp.com</a>

Surf and kitesurf schools have a problem that generic booking tools don't understand: the thing they're selling depends entirely on the weather. A yoga studio can take bookings weeks out without thinking about wind. A kitesurfing school can't. A lesson booked for Saturday morning might need to be rescheduled by Friday afternoon because the wind dropped, or cancelled because gusts are too strong for beginners.

Gusty is a booking management platform built specifically for water sports schools. It treats weather as a first-class piece of data, not an afterthought.

## The problem

Most schools I talked to were managing bookings through a mix of WhatsApp groups, manual spreadsheets, and generic tools like Google Forms or Calendly. Instructors got schedule updates over text. Students found out about cancellations last-minute. Equipment allocation was tracked on paper. The real problem wasn't finding students. It was everything that happened after the booking.

## What Gusty does

Schools get a dashboard to manage instructors, students, bookings, and equipment. Weather is integrated throughout: when a forecast crosses a school's configured thresholds for a given skill level, affected bookings are automatically flagged as "needs reschedule." An advanced student can handle stronger wind but a beginner can't, and the system handles that per-booking rather than per-day.

Equipment allocation is smart too. Boards and gear are suggested automatically based on the student's weight, skill level, and the forecasted wind conditions, then confirmed by the instructor before the session.

Every school gets a public mini-site at `{school}.gustyapp.com`, a clean booking page that also works as an embeddable widget for schools that want to drop it into their own website. QR codes let offline touchpoints like beach signs and flyers feed directly into the online booking flow.

Communication goes through WhatsApp via the Meta Cloud API, because that's where students already are and it's what school owners actually check. Reschedule notifications, booking confirmations, and reminders all go there.

## Architecture

The backend is FastAPI with SQLAlchemy on top of Supabase Postgres. Auth is handled by Supabase on the frontend, with JWTs verified by the API on every request. All data flows through the API, and the frontend only touches Supabase directly for authentication.

Multi-tenancy is enforced at the database level via `school_id` on every table, with Supabase row-level security as an additional layer. Subscriptions gate features per school, with per-school overrides for edge cases.

There are four distinct frontend surfaces in one Next.js codebase: the school management dashboard, an instructor-facing view (minimal schedule + weather, separate bundle), a platform admin panel for Gusty-level operations, and the public mini-site. The mini-site is statically generated with ISR so it loads in under two seconds even on a slow beach connection.

Payments go through Stripe Connect. Schools connect their own Stripe accounts, students pay the school directly, and Gusty takes a platform fee. Apple Pay and Google Pay are supported via Stripe Checkout.

**Stack:** Next.js (App Router) · React · TypeScript · FastAPI · SQLAlchemy · Supabase (Postgres + Auth + Storage) · Open-Meteo · Stripe Connect · Meta WhatsApp Cloud API · Resend · Vercel
