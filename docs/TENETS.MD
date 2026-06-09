# TENETS.MD - Core Principles
# ComposerAtlas

**Version:** 1.0
**Last Updated:** 2026-06-08

---

## Overview

These tenets guide every product, design, and technical decision made on ComposerAtlas. When trade-offs arise, consult this list. The order matters: higher tenets take precedence over lower ones.

---

## 1. Transparency Over Hype

Every strategy is presented with its full metrics, including drawdowns and risk figures. We never cherry-pick data to make a strategy look better than it is. Visitors should leave with an accurate picture, not an inflated one.

*If we are tempted to hide a metric because it looks bad, we show it anyway.*

---

## 2. Education Before Promotion

ComposerAtlas exists to teach first. Strategy pages explain the logic, signals, and reasoning behind every symphony. A visitor who understands why a strategy works is more valuable than one who blindly clones it.

*If we cannot explain a strategy in plain English, we do not feature it yet.*

---

## 3. Simplicity Over Complexity

Every feature should reduce friction, not add it. The site should feel like a well-organized wiki, not a bloated dashboard. When in doubt, remove the element.

*If a feature requires a tutorial to use, reconsider the feature.*

---

## 4. Zero Cost to Operate

ComposerAtlas runs on GitHub Pages with no server, no database service, and no paid infrastructure. Every technical decision must be evaluated against this constraint. Complexity that introduces operational cost is rejected at MVP.

*If it requires a server, find a static alternative.*

---

## 5. Data Is the Product

The strategy database is the most valuable asset of ComposerAtlas. Metrics must be accurate, schema must be consistent, and updates must be logged. A strategy with stale or incorrect data should be flagged or removed.

*If the data is wrong, the site is wrong.*

---

## 6. Design With Intention

Every visual decision should serve the user's ability to understand information. Color is used semantically (green = positive, pink = negative). Typography is used for hierarchy. White space is not wasted. We do not decorate for the sake of decoration.

*If an element does not aid comprehension, remove it.*

---

## 7. Independence and Integrity

ComposerAtlas is not affiliated with Composer.trade. We do not receive compensation for featuring any strategy. Our editorial choices are not for sale. If we ever establish a formal partnership or receive compensation, it will be disclosed prominently.

*If it creates a conflict of interest, disclose it or avoid it.*

---

## 8. Open and Maintainable

The codebase is public, readable, and maintainable by a single developer. We do not introduce dependencies or patterns that create lock-in or require specialist knowledge to maintain.

*If the next developer cannot understand it in 10 minutes, simplify it.*
