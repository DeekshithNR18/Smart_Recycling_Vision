Vision Module — Smart Recycling Assistant
Role: Computer Vision Engineer
Problem Statement: Smart Recycling Assistant

This is the computer vision component of our Smart Recycling Assistant. It handles the task of looking at an object through a webcam and determining which of four waste categories it belongs to. This document explains how it works, what we tested, and how other team members can use it.

What This Module Does

The module uses a webcam to identify a waste object and classify it into one of four bins.

Category — Examples
Recyclable — Plastic bottles, soda cans, glass jars, cardboard
Biodegradable — Apple cores, banana peels, food scraps
Hazardous — Batteries, e-waste, chemicals, expired medicine
Reusable — Intact headphones, phones, containers, clothing

Every scan returns three pieces of information:

The label (which bin it belongs to)
A confidence score (how sure the system is)
A short reasoning (a plain-English explanation)

Two Engines, One Experience

Rather than depending on a single AI model, the system uses a dual-engine architecture. It automatically selects the best engine available at any given moment.

Qwen 2.5 VL — The On-Device Engine

Runs locally on the machine, with no internet connection needed
No API quota, so you can scan indefinitely
Privacy-first, as images never leave the device
Speed: approximately 50 to 65 seconds per scan on CPU
Best used as a reliable fallback or for offline demos

Google Gemini — The Cloud Engine

Runs on Google's servers for fast classification
Speed: approximately 3 to 15 seconds per scan when quota is available
Free tier allows about 20 requests per day
Best used for fast demos when the daily quota is fresh

Automatic Failover

If Gemini is slow or has reached its quota, the system switches to Qwen automatically. The user still receives a result without any interruption.

Live Demo Features

Center-Square Region of Interest

The scanner analyzes only the central square of the webcam frame, ignoring background clutter. A visual bracket shows exactly what the AI is looking at.

Four Background Styles

Pressing B cycles through four ways of rendering the background:

Blurred grayscale, which makes the object stand out
Pure black, for maximum focus
Vignette, giving a softer, cinematic feel
Dimmed, the original muted style

Color-Coded Result Banners

The on-screen banner uses a different color for each category, making the result instantly readable.

Live Session Statistics

A small heads-up display shows real-time counts of every category scanned during the current session.

Automatic Saving

Every scan is saved as an image, with the label and confidence written into the filename. Each result is also appended to a JSONL log file for downstream processing.

Accuracy and Verified Data

The system was tested against 43 carefully selected objects across all four categories. Every single one classified correctly.

Test Suite Summary

Test Suite — Items Tested — Passed — Accuracy
Demo Presets — 9 — 9 — 100%
Broad Coverage — 25 — 25 — 100%
Face and Person Skip — 6 — 6 — 100%
Override Logic — 3 — 3 — 100%
Total — 43 — 43 — 100%

Broad Coverage Results (25 of 25)

Object — Correct Classification
Apple — Biodegradable
Banana peel — Biodegradable
Coffee grounds — Biodegradable
Used tissue — Biodegradable
Notebook — Recyclable
Glass bottle — Recyclable
Steel spoon — Recyclable
Newspaper — Recyclable
Yogurt cup — Recyclable
Milk carton — Recyclable
AA battery — Hazardous
Broken laptop — Hazardous
Dead phone — Hazardous
Expired medicine — Hazardous
Paint can — Hazardous
Engine oil — Hazardous
Working headphones — Reusable
Wooden chair — Reusable
Sock — Reusable
Backpack — Reusable
USB cable — Reusable
Old TV — Reusable
Sunglasses — Reusable
Bicycle — Reusable
Leather wallet — Reusable

What Makes This Module Smart

Over 400 Keyword Override Rules

The system does not rely on AI reasoning alone. More than 400 specific patterns have been encoded across all four categories. If the AI says "Recyclable" for a battery, the override catches it and corrects it to "Hazardous."

Damage and E-Waste Detection

An intact phone is Reusable. A broken phone is Hazardous because it becomes e-waste. The system detects keywords such as broken, swollen, dead, expired, cracked, and leaking, and routes them to the correct category.

Smart Device Rules

Devices get special treatment. If a device is intact and functional, it is classified as Reusable. If it is broken or dead, it becomes Hazardous.

Face and Person Skip

The scanner does not misclassify people as waste. If the AI sees a face, a wall, or an empty scene, the scan is skipped and a "no object detected" message is shown.

Two-Stage Reasoning for Qwen

Qwen does not classify objects blindly. It first identifies the object, then classifies it based on that identification. This two-step process produces much higher accuracy than attempting to do both at once.

Confidence Gating

If the model returns a result below 50% confidence, the system retries with more context. Low-quality answers do not pass through.

LRU Cache

Identical images are cached. Rescanning the same object returns instantly, using zero API calls.

