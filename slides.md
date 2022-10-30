# Passaging

![](./title.excalidraw.png)

<span style="display: flex">
  <span>David Laban</span>
  <span style="flex-grow: 1"> </span>
  <span>31 Oct 2022</span>
</span>

---

# Outline

- ...

- Questions

---

# Problem Statement

Perform passaging, when conditions are met:

* Take images of flask periodically.
* Wait for a condition to be met.
* Move cells to new flask.

User may review images and delay passaging at any point.

---

# Failure Modes

* User is unresponsive:
  * Continue with the passaging.
  * Alert the user.
* Network Outage:
  * Continue with the passaging.
  * Alert the user.

???

Keep Trucking On.

---

# High Level Architecture

![](./high-level.excalidraw.png)


---

# Growth Device

![](./growth-device.excalidraw.png)


---

# Server Infrastructure - Distributed Monolith

![](./server-infrastructure.excalidraw.png)

---

# Server Infrastructure - Push Based

* TODO

???

* I know that they are using MQTT. Why?
  * Immediate control of device from laptop/tablet?
    * Aside: Do you even allow laptops in labs? Feels like a contamination magnet.
  * Efficiency doesn't feel like the driving factor.
* 

---

# Device Enrolment

* TODO

???

* Provide wifi creds via hotspot dance || require wired connection
  * Okay to require wired connection for large installs
* Device doesn't seem to have a screen


---

# ...

---

# Questions?


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

# Appendix: Wardley Map

![](./wardley-map.excalidraw.png)

???

TODO: add server racks - should not be bespoke

---

# Appendix: Personal Notes

* [Video: Passaging Cells: Cell Culture Basics](https://www.youtube.com/watch?v=CMRKKl9XSDU)

---

# Appendix: UI

![](./ui.png)
