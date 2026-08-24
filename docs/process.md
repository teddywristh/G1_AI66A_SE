# Software Process - Mini Marketplace

## Section 1 - Chosen Process and Its Position on the Spectrum

### (a) The Model: Incremental Development with Prototyping

We adopt an **Incremental model** with **Prototyping** used during the first increment to validate the core order-flow design. The project is delivered in three increments, each producing a working, deployable slice of the system:

| Increment | Scope | Duration |
|-----------|-------|----------|
| 1 | User authentication, role-based access (buyer / seller / admin), product CRUD for sellers, and a throwaway UI prototype of the checkout flow to validate the order state machine. | ~3 weeks |
| 2 | Shopping cart, order placement, order management dashboard (buyer views orders, seller updates status), and concurrent-stock handling. | ~3 weeks |
| 3 | Revenue analytics, best-selling product statistics, admin panel, UI polish, and final integration testing. | ~3 weeks |

Within each increment the cycle runs as: **requirements refinement → design → implementation → testing → internal demo**. The team lead assigns tasks at the start; developers work on feature branches and merge through reviewed Pull Requests. At the end of each increment we hold a brief retrospective to adjust the plan for the next one.

### (b) The Position: ~70 % Agile, 30 % Plan-Driven

We sit closer to the agile end because requirements are still emerging (the instructor clarifies expectations incrementally) and the two-person team can coordinate with near-zero communication overhead. However, we retain plan-driven elements where volatility is low:

* **Frozen for the semester:** the high-level feature list (products, cart, orders, analytics), the technology stack, the database schema's core tables, and the four course milestones.
* **Re-opened every increment:** UI layout, API endpoint signatures, analytics chart choices, and task priority within the backlog.

This hybrid lets us lock architectural decisions early (avoiding rework) while remaining free to re-order or reshape individual features as we learn more from each working increment.

---

## Section 2 - The Five Diagnostic Questions

**1. Are your requirements stable or volatile?**
Moderately volatile. The project brief fixes the top-level capabilities (product listing, cart, orders, analytics), but the detailed behaviour - e.g., what statistics count as "revenue analytics," how order cancellation works, whether sellers can edit an order after confirmation - is not specified. Evidence: at least three clarification questions were raised during early discussion, and the instructor confirmed that teams may interpret these details freely. This volatility favours shorter feedback loops rather than a full upfront specification.

**2. Does the project carry safety or legal impact?**
No. The mini marketplace handles no real money, stores no genuine personal data, and will never be deployed to production users. Therefore we do not need formal change-control boards, regulatory traceability matrices, or independent verification. Lightweight documentation (this dossier, inline code comments, PR descriptions) is proportionate to the risk.

**3. Is the team large and distributed, or small and co-located?**
Small (two members) and semi-co-located: we attend the same classes and can pair-program in person, but also collaborate asynchronously via GitHub and messaging. With only two developers, the communication cost is minimal - one communication channel, no need for daily stand-ups or a Scrum Master role. This makes agile-style informal coordination cheap and effective.

**4. Can the customer engage continuously or only at fixed checkpoints?**
Primarily at fixed checkpoints. The instructor reviews work at four course milestones and the final demo. Between milestones, we can ask questions during class or office hours, but sustained daily feedback is not available. We compensate by using the end-of-increment internal demo as a self-review checkpoint, running the application ourselves as both buyer and seller to surface usability issues before the official milestone.

**5. What do organizational culture and contract constraints allow?**
The course mandates four fixed milestones and a final demo date. These act as plan-driven gates that we cannot move. Between gates, however, we are free to choose any internal rhythm. Our constraint is time-boxed increments aligned to the milestone calendar, so our process must deliver a demonstrable product at each gate while remaining flexible inside the time-box.

---

## Section 3 - Critical Thinking: Risks of the Opposite Choice

If we had adopted a **fully plan-driven Waterfall** approach - completing all requirements, then all design, then all implementation, then all testing in strict sequence - the single biggest risk would be **late discovery of integration failures in the order flow**.

**Mechanism:** The mini marketplace's order pipeline involves three roles (buyer, seller, admin) interacting through shared state (order status, stock count). A Waterfall process would defer integration testing to the final phase. With only two developers building separate layers in isolation for weeks, mismatches between the front-end order state machine and the back-end stock-concurrency logic would remain invisible until the integration phase. At that point, fixing a fundamental design mismatch (e.g., the front-end assumes optimistic stock reservation while the back-end implements pessimistic locking) requires reworking both layers under deadline pressure.

**First observable symptom:** During the late integration phase, automated or manual tests would reveal that placing two simultaneous orders for the last item in stock sometimes succeeds for both buyers - a classic concurrency defect that would have been caught in Increment 1 under our incremental approach, where the order flow is prototyped and tested end-to-end early.

---

## Section 4 - Process Rules the Team Commits To

1. **Pull-Request gate:** Every change reaches `main` through a Pull Request reviewed and approved by at least one other team member. Self-approved PRs are not permitted.

2. **Increment cadence:** Each increment is approximately three weeks long and ends with a commit-tagged internal demo (tag format: `increment-N`). The backlog is re-prioritized at the start of each increment.

3. **Change logging:** Any requirement change or scope adjustment after an increment has started is recorded in `docs/changelog.md` with the date, description, and reason for the change.

4. **Branch naming convention:** All feature work uses the pattern `feature/<member-name>/<short-description>` (e.g., `feature/NhatMinh/order-api`). Documentation branches use `docs/<topic>`. No direct commits to `main`.

5. **Testing before merge:** Every PR must include evidence that the changed feature was tested — either automated test results in the PR description or a screenshot/recording of manual testing. PRs without test evidence will be rejected during review.
