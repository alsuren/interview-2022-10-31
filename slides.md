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

Keep Trucking On.

---

# High Level Architecture

![](./high-level.excalidraw.png)


---

# Passaging Device

![](./passaging-device.excalidraw.png)


---

# ...

---

# Questions?


---

# Appendix A: Fail-Safe Behaviour

* What should the behaviour be if the user is unresponsive?
  * Probably continue with the passaging?
  * Alert the user that this is what you've done?
* What should the behaviour be if the server is unreachable?
  * Probably continue with the passaging?
  * Alert the user that the device is unavailable.


---

# Appendix B: Wardley Map

![](./wardley-map.excalidraw.png)
