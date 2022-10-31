# Passaging

![](./title.excalidraw.png)

<span style="display: flex">
  <span>David Laban</span>
  <span style="flex-grow: 1"> </span>
  <span>31 Oct 2022</span>
</span>

---

# Outline

* Problem Statement
* What Could Go Wrong?
* High Level Architecture
  * Growth Device
  * Server Infrastructure
* Questions

---

# Problem Statement

Automate passaging for cell growth.

* Take microscope images of cells.
* Wait for conditions to be met.
* Move cells to new flask.
  * Check cell health.
* Repeat.

User can review images and override.

---

# What Could Go Wrong?

What should we do?

* User is unresponsive?
  * Continue the process. Alert the user.
* Network Outage?
  * Continue the process. Alert the user.
* Hardware Fault?
  * <s>Continue the process.</s> Alert the user.

Keep Trucking On. Don't kill the cells.

---

# High Level Architecture

![](./high-level.excalidraw.png)

???

* Map of the landscape.
* Will dig into:
  * Growth Device
  * Server Infrastructure.

---

# Growth Device

![](./growth-device.excalidraw.png)

???

* Needs to be autonomous
  * Microscope Focus
  * Confluence Estimation
  * Cell liveness estimation
  * User-provided strategy
* Motors control everything: pumps, flask selection, (centrifuges?)
  * Motor control wants to be on its own RTOS
  * Talked to Henry about protocol.
  * Otherwise, protobuf/..., bind into both languages.
  * Add checksums, and fuzz in CI.
* Pick commodity-off-the-shelf microscopes
  * Focussing is the only thing that wants to be real-time.
    * Simple maximum-sharpness algorithm would be enough for this.
    * Could be built into the microscope?

---

# Server Infrastructure A - Distributed Monolith

![](./server-infrastructure.excalidraw.png)

???

* Growth Device
  * Photos uploaded to blob storage like S3.
  * Status updates to Device endpoints
  * Poll for user overrides
* User Laptop
  * Probably GraphQL for everything
  * Email/SMS notifications might go via a queue.
* Distributed Monolith
  * Shared DB
  * Web Tier + Async Tier
* Assessment
  * This would probably work
      * Timescales are slow enough.
      * Device Polls for overrides before acting.
  * Henry says you are using MQTT. Why?
      * Immediate control of device from laptop/tablet?
      * (Efficiency doesn't feel like the driving factor)

---

# Server Infrastructure B - Push Based

![](./server-push.excalidraw.png)

???

* Responsive control from laptop.
* External push service.
  * Pubsub topics/channel(s) per device
  * Pubsub topics/channel(s) per user
  * Email/SMS can hang off the push service too.

---

# Server Infrastructure C - Message Broker

![](./server-mqtt-nats.excalidraw.png)

???

* Rip everything out and use a message broker.
* Logical message flow is more direct.
  * Messages flow "directly" from Device to Laptop.
  * Some topics are durable.
  * Others use Request/Response abstraction.
* Everything is connected to the message broker (dashes)
* Broker
  * MQTT / NATS / ...
  * Handles fan-out / load-balancing.
  * Handles access control (needs help).
  * Stores messages for offline clients if desired.
  * Message size limits, so GraphQL still needed
      * Not sure about the state of graphql subscriptions
* Not Shown:
  * Auth Service for bootstrapping Device and Laptop
  * Blob Storage for images.
  * Distributed tracing infrastructure for debugging.

---

<h1 style="position: relative; top: 40%; text-align: center;">Questions?</h1>
---

# Appendix: Things Not Covered

* Auth Service for Device + Laptop.
* Device Provisioning Flow.
* Device Update Infrastructure.
* Distributed Tracing infrastructure.

???

* Make JWT signed for $topic-prefix/#
* oauth URL + display short auth code dance
* openembedded(?) || silverblue + containers
* Opentelemetry (correlation ids in mq headers)


---

# Appendix: Wardley Map

![](./wardley-map.excalidraw.png)

---

# Appendix: Assumptions

* Cells move slowly
  * Low frame rate
  * Okay to upload every photo to S3.
* Microscope can autofocus with simple max-sharpness algorithm
* Linux board is pretty powerful
  * Can handle HTTP+SSL just fine
* Fleet size is small-ish (risky)
  * HTTP polling is just fine

---

# Appendix: Personal Notes

* [Video: Passaging Cells: Cell Culture Basics](https://www.youtube.com/watch?v=CMRKKl9XSDU)

---

# Appendix: Cell Review UI

![](./ui.png)

---

# Appendix: Dashboard UI

![](./dashboard.png)

---

# Device Enrolment

* TODO

???

* Provide wifi creds via hotspot dance || require wired connection
  * Better to require wired connection for large installs
* Device doesn't seem to have a screen:
  * Indicator LEDs for "please feed me"?
  * ID printed on the front, or QR code somewhere?
