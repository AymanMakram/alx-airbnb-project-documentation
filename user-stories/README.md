#  User Stories Explanation: Airbnb Clone Backend Requirements

This document explains the purpose and structure of the **User Stories** file (`user-stories.md`) within the project, which translates high-level functional needs into actionable development requirements.

---

## What are User Stories?

User stories are informal, natural language descriptions of features written from the perspective of an end-user or system actor. They follow a simple template:

> **As a [Role], I want to [Goal] so that [Benefit].**

They serve as the primary link between the **business objectives** (what the app needs to do) and the **technical implementation** (what the developers need to build).

---

## Connection to Use Case Diagram

The user stories were derived directly from the Use Case Diagram, ensuring that every critical interaction between the system and its actors (Guest, Host) is captured:

1.  **Actor Mapping:** Each "Role" in the story corresponds to an **Actor** (e.g., Guest, Host).
2.  **Functionality Mapping:** The "Goal" in the story corresponds to a **Use Case** (e.g., "Book Property," "Create Listing").

This process ensures **traceability**, meaning every feature implemented can be traced back to a specific user need.

---

## Breakdown of the 5 Core User Stories

The five core stories focus on the most critical paths through the application:

| Story Type | Corresponding Use Case | Why It's Critical |
| :--- | :--- | :--- |
| **US-001: Registration** | `Register Account` | Ensures system access and differentiates user roles (RBAC). |
| **US-002: Listing** | `Create Listing` | Essential for building the marketplace inventory (Host core function). |
| **US-003: Search** | `Search Properties` | Defines the Guest's primary method for discovery and navigation. |
| **US-004: Booking/Payment** | `Book Property` & `Make Payment` | Represents the core revenue-generating transaction flow. |
| **US-005: Feedback** | `Leave Review & Rating` | Establishes the quality and trust mechanism for the entire platform. |