# Mobile Application Technical Requirements

## 1. Purpose and course context

This document converts the analyst's course requirements into implementation-ready requirements for an Android mobile application. The final application must demonstrate that the team can design a mobile UI, manage application data, use external services, and support common mobile conditions such as rotation and temporary loss of network connectivity.

The application must focus on one primary concept: e-commerce, a news platform, or a social network. The team should complete one core flow instead of implementing many incomplete features.

## 2. Learning-outcome coverage

### Knowledge

- **CLO1.1:** Explain mobile concepts demonstrated by the app, including screens, navigation, lifecycle, responsive layouts, authentication, online/offline operation, and API communication.
- **CLO1.2:** Identify and explain the UI/UX widgets used, including lists, cards, text fields, buttons, images, menus, dialogs, bottom navigation, loading indicators, and pull-to-refresh controls.
- **CLO1.3:** Explain how remote data is stored in Firebase, how it is cached locally with SQLite, and how the app chooses between each data source.

### Skills

- **CLO2.1:** Build usable screens with mobile widgets, consistent spacing, clear hierarchy, validation, navigation, and feedback for loading, success, empty, error, and offline states.
- **CLO2.2:** Integrate Firebase services and at least one supporting API or library, such as Google Sign-In, image loading, remote content, or connectivity monitoring.

## 3. Users and permissions

### Guest

- Browse public content and view pages cached on the device.
- Sign in before creating content, placing an order, or saving private data.

### Authenticated user

- Manage their profile and use the main workflow for the selected concept.
- Create, edit, and delete only content they own.
- Sign out and use Google account authentication if enabled.

### Admin

- Use a simple mobile-friendly management flow, not a separate desktop dashboard.
- View, create, edit, hide, or delete relevant content.
- Confirm destructive actions.
- Access only features allowed by their role. Role checks must be enforced by Firebase security rules, not only by hiding UI buttons.

## 4. Required screens and UX behavior

The application must include:

- A first-launch/sign-in flow.
- A main screen containing the primary list or feed.
- A detail screen for a selected item.
- A create/edit form for the main user action.
- Profile and sign-out access.
- Consistent navigation using bottom navigation, a drawer, or another mobile pattern.
- Loading indicators during remote requests.
- Empty-state messaging when no records exist.
- Inline validation for required and invalid fields.
- Error messages with a retry action.
- Confirmation dialogs for delete, logout, and other irreversible actions.
- Pull-to-refresh or an equivalent fresh-data action.

The UI must handle loading, empty, error, success, and offline states explicitly. It must not leave the user on a blank screen or report an operation as successful before it has been saved.

## 5. Functional requirements

### Authentication

1. Support email/password or Google Sign-In.
2. Show the signed-in user's name and email where appropriate.
3. Preserve the session between app launches.
4. Clear protected local state on sign-out and return to the guest flow.
5. Display readable authentication errors without exposing technical details.

### Content management

1. Browse a Firebase-backed list of the selected domain's records.
2. Open a record and view its complete details.
3. Authenticated users can create records using a validated form where applicable.
4. Owners can edit or delete their own records.
5. Admins can moderate records according to their permissions.
6. Use pagination or incremental loading as the dataset grows.
7. Show created or updated records without requiring an app restart.

### Minimum domain flow

- **E-commerce:** browse products → view product → add to cart → review cart/order summary.
- **News:** browse categories/articles → view article → save/bookmark or share.
- **Social network:** browse feed → create post → view post → edit/delete own post.

### Admin flow

1. Provide a clearly identified admin screen.
2. Show each record's key information, status, and available actions.
3. Reuse the same validation rules as user forms where applicable.
4. Require confirmation before delete or hide actions.
5. Reflect admin changes in user-facing content after synchronization.

## 6. Backend and data requirements

Firebase is mandatory and should include:

- **Firebase Authentication** for accounts and Google Sign-In where configured.
- **Cloud Firestore** for structured data such as users, posts, products, articles, carts, or orders.
- **Firebase Storage** for uploaded images or other media.
- **Firebase security rules** for authentication, ownership, and admin permissions.

Records should include a unique ID, owner/author ID, created timestamp, updated timestamp, status, and the fields required by the chosen concept.

### Local/offline behavior

SQLite is recommended for the main list/detail cache.

- Cache the most recently successful data request.
- Show cached records when offline and indicate that data may be stale.
- Queue writes or disable them offline; never claim an unsent write succeeded.
- Retry synchronization when connectivity returns.
- Document the conflict rule, such as server-last-write-wins or explicit user review.
- Never store passwords or sensitive tokens in plain SQLite tables.

## 7. Responsive and device requirements

- Support portrait and landscape without clipped, overlapping, or inaccessible content.
- Preserve the current screen and form data during rotation where appropriate.
- Use responsive constraints/layouts rather than fixed coordinates.
- Provide readable labels and adequately sized touch targets.
- Support scrolling for long lists and forms.
- Handle the keyboard without hiding the active input or submit button.
- Test at least one small phone layout and one larger phone/tablet-like layout.

## 8. API and library integration

Libraries and APIs must be used in real features, not merely added as dependencies. Suitable integrations include Firebase services, Google authentication, an image-loading/cache library, a connectivity observer, or a relevant remote API.

Network calls must handle timeout, no connection, malformed responses, authorization failures, and server failures.

## 9. Suggested Android architecture

Separate responsibilities so UI code does not contain all database logic:

- **UI layer:** screens, widgets, navigation, validation, and UI state.
- **ViewModel/state layer:** screen state, user actions, loading state, and rotation-safe data.
- **Repository/data layer:** Firebase access, SQLite access, caching, and synchronization.
- **Model layer:** domain and database models.

Firebase configuration and other secrets must stay out of source control where possible. Document the setup required for another developer to run the app.

## 10. Security and quality

- Never hard-code passwords, private keys, or user secrets.
- Validate input in the client and enforce permissions with Firebase rules.
- Do not trust an admin flag supplied only by the client.
- Do not log passwords, tokens, or private user data.
- Prevent duplicate submissions while a request is in progress.
- Use stable IDs and timestamps for reliable synchronization.

## 11. Acceptance criteria

The application is complete when:

- It installs and launches on an Android device or emulator.
- The selected domain's minimum flow works from start to finish.
- Firebase authentication and remote data operations work with valid credentials.
- Unauthorized users cannot perform protected operations.
- The simple admin flow can manage the required records.
- The app remains usable after device rotation.
- Previously loaded pages remain readable when the network is disabled.
- Loading, empty, validation, error, and offline states are understandable.
- At least one supporting library/API is integrated into a real feature.
- The team can explain how widgets, Firebase, SQLite, synchronization, and APIs satisfy CLO1.1–CLO2.2.
