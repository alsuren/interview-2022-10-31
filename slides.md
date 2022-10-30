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
* Failure Modes
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

User should review images and may override.

---

# Failure Modes

* User is unresponsive:
  * Continue the process.
  * Alert the user.
* Network Outage:
  * Continue the process.
  * Alert the user.

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

# High Level Architecture Recap

![](./high-level.excalidraw.png)

???

* Talk about Server Architecture Next

---

# Server Infrastructure - Distributed Monolith

![](./server-infrastructure.excalidraw.png)

???

* Shared DB
* Web Tier + Async Tier
* Photos uploaded to blob storage like S3.
* This would probably work
  * Timescales are slow.
  * Poll for overrides before acting.
* I know that they are using MQTT. Why?
  * Immediate control of device from laptop/tablet?
  * Efficiency doesn't feel like the driving factor.

---

# Server Infrastructure - Push Based

![](./server-push.excalidraw.png)

???

* I know that they are using MQTT. Why?
  * Immediate control of device from laptop/tablet?
* External push service - infrastructure.
  * Pubsub topics/channel(s) per device
  * Pubsub topics/channel(s) per user
  * Can often hang email/sms off the push service.

---

# Server Infrastructure - Message Broker

![](./server-mqtt-nats.excalidraw.png)

???

* Dashed lines depict that everything is connected to the message broker
  * Broker = MQTT / NATS / ...
  * Physical topology not as important as logical message flow.
  * Re: Access Control:
      * Devices+Laptops should only be able to subscribe to topics from the same org
* Logical message flow is more direct.
* Broker handles fan-out / load-balancing.
* Broker stores for offline clients if desired.
* Brokers have message size limits, so GraphQL still needed.
* Not Shown:
  * Auth Service for bootstrapping Device and Laptop
  * Blob Storage for images.
  * Distributed tracing infrastructure for debugging.

---

<h1 style="position: relative; top: 40%; text-align: center;">Questions?</h1>

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

# Appendix: Fail-Safe Behaviour

* What should the behaviour be if the user is unresponsive?
  * Probably continue with the passaging?
  * Alert the user that this is what you've done?
* What should the behaviour be if the server is unreachable?
  * Probably continue with the passaging?
  * Alert the user that the device is unavailable.


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
