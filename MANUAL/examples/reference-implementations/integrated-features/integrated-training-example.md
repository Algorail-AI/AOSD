# Integrated Training Reference Implementation

**AOSD Level 2 Example - Embedding Training into Applications**

> **Note**: This is a reference implementation showing how AOSD Goal 6 (Embed Training and Knowledge Directly Into Applications) can be achieved. Adapt these patterns for your specific application and user base.

---

## 1. Purpose

This document demonstrates how to build applications that train their users directly, reducing reliance on external documentation, manuals, or training sessions.

The goal is applications where:
- Users learn by doing, with guidance embedded in the workflow
- Help is contextual and immediate
- Documentation stays current because it's part of the application
- Onboarding is integrated, not separate
- User skill progression is supported

---

## 2. Core Principles

### 2.1 Progressive Disclosure

Don't overwhelm users with all information at once:

```
Level 1: Essential - What users must know to complete task
Level 2: Helpful - Tips and shortcuts
Level 3: Advanced - Power user features
Level 4: Expert - Customization and edge cases
```

### 2.2 Contextual Relevance

Information appears when and where it's needed:

```
┌─────────────────────────────────────────────────────────┐
│ User Action                    │ Training Response      │
├─────────────────────────────────────────────────────────┤
│ First time using feature       │ Guided walkthrough     │
│ Hovers over unfamiliar term    │ Inline definition      │
│ Makes common mistake           │ Corrective guidance    │
│ Completes task successfully    │ "What's next" pointer  │
│ Stuck for > 30 seconds         │ Proactive help offer   │
└─────────────────────────────────────────────────────────┘
```

### 2.3 Learning Retention

Help users remember what they've learned:

- Show tips once, track dismissal
- Reinforce concepts through repetition across features
- Celebrate milestone completions
- Provide easy way to re-access dismissed help

---

## 3. Pattern: Contextual Help System

### 3.1 Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      APPLICATION UI                          │
│  ┌─────────────────────────────────────────────────────┐   │
│  │  Feature Component                                   │   │
│  │  ┌───────────────────────────────────────────────┐  │   │
│  │  │ Input Field                           [?]     │  │   │
│  │  └───────────────────────────────────────────────┘  │   │
│  │                           │                          │   │
│  │                           ▼                          │   │
│  │  ┌───────────────────────────────────────────────┐  │   │
│  │  │ Contextual Help Popup                         │  │   │
│  │  │                                               │  │   │
│  │  │ "This field accepts email addresses..."       │  │   │
│  │  │                                               │  │   │
│  │  │ [Show me an example]  [Don't show again]      │  │   │
│  │  └───────────────────────────────────────────────┘  │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    HELP CONTENT SERVICE                      │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────────┐  │
│  │ Static Help │  │ User Context │  │ AI Enhancement    │  │
│  │ Content     │  │ (skill level)│  │ (if enabled)      │  │
│  └─────────────┘  └──────────────┘  └───────────────────┘  │
└─────────────────────────────────────────────────────────────┘
```

### 3.2 Help Content Structure

```yaml
# help-content/user-registration.yaml
feature: "user_registration"
context: "email_field"

levels:
  essential:
    text: "Enter your email address"
    trigger: "focus"

  helpful:
    text: "We'll use this for account recovery and notifications"
    trigger: "hover_help_icon"

  advanced:
    text: "You can use email aliases (yourname+tag@domain.com)"
    trigger: "explicit_request"
    show_after_uses: 3

examples:
  - valid: "user@example.com"
    explanation: "Standard email format"
  - valid: "user+newsletter@example.com"
    explanation: "Email alias for filtering"
  - invalid: "user@example"
    explanation: "Missing domain extension"

common_errors:
  - error_pattern: "invalid_format"
    help: "Make sure your email includes @ and a domain like .com or .org"
  - error_pattern: "already_exists"
    help: "This email is already registered. Try logging in or use password reset."
```

### 3.3 Implementation Example

```python
# services/contextual_help.py

class ContextualHelpService:
    def __init__(self, content_store, user_context_service):
        self.content = content_store
        self.user_context = user_context_service

    def get_help(self, feature: str, context: str, user_id: str) -> HelpContent:
        """Get appropriate help content for user and context."""
        base_content = self.content.get(feature, context)
        user_level = self.user_context.get_skill_level(user_id, feature)
        user_history = self.user_context.get_help_history(user_id, feature, context)

        # Progressive disclosure based on user level
        if user_level == "new":
            return base_content.essential
        elif user_level == "familiar":
            if not user_history.has_seen("helpful"):
                return base_content.helpful
            return None  # User knows this, don't show
        else:  # advanced
            if user_history.requested_advanced:
                return base_content.advanced
            return None

    def get_error_help(self, feature: str, error_type: str) -> str:
        """Get help for specific error."""
        content = self.content.get(feature, "common_errors")
        for error in content:
            if error.error_pattern == error_type:
                return error.help
        return "An error occurred. Please try again."

    def record_help_shown(self, user_id: str, feature: str, context: str, level: str):
        """Track that help was shown to user."""
        self.user_context.record_help_view(user_id, feature, context, level)
```

---

## 4. Pattern: AI-Assisted Onboarding

### 4.1 Onboarding Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    ONBOARDING FLOW                           │
│                                                              │
│  Step 1: Welcome                                            │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ "Welcome! I'll help you get started."               │   │
│  │                                                      │   │
│  │ What's your main goal with this application?        │   │
│  │ [ ] Manage projects                                  │   │
│  │ [ ] Track time                                       │   │
│  │ [ ] Collaborate with team                            │   │
│  │ [ ] Generate reports                                 │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                 │
│                            ▼                                 │
│  Step 2: Personalized Path                                  │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Based on your goals, here's your learning path:     │   │
│  │                                                      │   │
│  │ 1. Create your first project (5 min)                │   │
│  │ 2. Add team members (3 min)                          │   │
│  │ 3. Set up time tracking (4 min)                      │   │
│  │                                                      │   │
│  │ [Start with Project Creation]                        │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                 │
│                            ▼                                 │
│  Step 3: Guided Task                                        │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ Creating Your First Project                         │   │
│  │ ─────────────────────────────                       │   │
│  │                                                      │   │
│  │ → Click the "New Project" button                    │   │
│  │   [Highlighting button in UI]                        │   │
│  │                                                      │   │
│  │ 💡 Projects help you organize related tasks         │   │
│  │                                                      │   │
│  │ [Skip this step]  [I need help]                      │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 4.2 AI Enhancement for Onboarding

When AI is available, enhance onboarding with:

```python
# services/ai_onboarding.py

class AIOnboardingEnhancer:
    def __init__(self, ai_service, base_onboarding):
        self.ai = ai_service
        self.base = base_onboarding

    async def personalize_welcome(self, user_profile: dict) -> str:
        """Generate personalized welcome message."""
        prompt = f"""
        Generate a brief, friendly welcome message for a new user.

        User context:
        - Role: {user_profile.get('role', 'unknown')}
        - Industry: {user_profile.get('industry', 'unknown')}
        - Team size: {user_profile.get('team_size', 'unknown')}

        Requirements:
        - 2-3 sentences max
        - Professional but warm
        - Mention one relevant feature for their context
        """

        try:
            response = await self.ai.generate(prompt, max_tokens=100)
            return response.text
        except AIServiceUnavailable:
            return self.base.get_default_welcome()

    async def answer_onboarding_question(self, question: str, context: dict) -> str:
        """Answer user question during onboarding."""
        prompt = f"""
        A new user asked this question during onboarding: "{question}"

        Current onboarding step: {context.get('step')}
        Application features: {context.get('features')}

        Provide a helpful, concise answer (2-4 sentences).
        If the question is off-topic, gently redirect to the onboarding.
        """

        try:
            response = await self.ai.generate(prompt, max_tokens=150)
            return response.text
        except AIServiceUnavailable:
            return "I'm having trouble answering right now. " + \
                   self.base.get_faq_response(question)
```

### 4.3 Onboarding Progress Tracking

```python
# models/onboarding_progress.py

class OnboardingProgress:
    user_id: str
    started_at: datetime
    completed_at: Optional[datetime]
    learning_path: List[str]  # Personalized based on goals

    steps_completed: List[OnboardingStep]
    current_step: Optional[str]

    questions_asked: List[OnboardingQuestion]
    help_accessed: List[HelpAccess]

    def completion_percentage(self) -> float:
        return len(self.steps_completed) / len(self.learning_path) * 100

    def time_spent(self) -> timedelta:
        end = self.completed_at or datetime.now()
        return end - self.started_at

    def stuck_detection(self) -> bool:
        """Detect if user seems stuck on current step."""
        if not self.current_step:
            return False
        step_start = self.steps_completed[-1].completed_at if self.steps_completed else self.started_at
        return (datetime.now() - step_start) > timedelta(minutes=5)
```

---

## 5. Pattern: In-App Documentation

### 5.1 Documentation Structure

```
┌─────────────────────────────────────────────────────────────┐
│                    IN-APP DOCUMENTATION                      │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ LAYER 1: Inline Help                                │   │
│  │ - Tooltips                                          │   │
│  │ - Field descriptions                                 │   │
│  │ - Error messages with guidance                       │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                 │
│                            ▼                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ LAYER 2: Contextual Panels                          │   │
│  │ - Feature explanations                               │   │
│  │ - Step-by-step guides                                │   │
│  │ - Examples and templates                             │   │
│  └─────────────────────────────────────────────────────┘   │
│                            │                                 │
│                            ▼                                 │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ LAYER 3: Searchable Knowledge Base                  │   │
│  │ - Full documentation                                 │   │
│  │ - FAQ                                                │   │
│  │ - Troubleshooting guides                             │   │
│  │ - AI-powered search                                  │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

### 5.2 Keeping Documentation Current

**Challenge**: External documentation drifts from application behavior.

**Solution**: Documentation lives with the code.

```yaml
# components/project-settings/docs.yaml
component: "project_settings"
version: "1.3.0"  # Matches component version

sections:
  overview:
    title: "Project Settings"
    content: |
      Configure how your project behaves, who can access it,
      and how notifications are sent.

  fields:
    project_name:
      label: "Project Name"
      help: "A descriptive name for your project (3-50 characters)"
      validation_rules:
        - "Must be 3-50 characters"
        - "Can include letters, numbers, and hyphens"

    visibility:
      label: "Visibility"
      help: "Controls who can see this project"
      options:
        private: "Only project members can see this project"
        team: "All team members can see this project"
        public: "Anyone with the link can view (not edit)"

  tutorials:
    - id: "first_project_setup"
      title: "Setting Up Your First Project"
      steps:
        - "Enter a descriptive project name"
        - "Choose visibility (Private recommended for new projects)"
        - "Add team members"
        - "Click Save"
```

### 5.3 AI-Enhanced Documentation Search

```python
# services/doc_search.py

class AIDocumentationSearch:
    def __init__(self, doc_index, ai_service):
        self.index = doc_index
        self.ai = ai_service

    async def search(self, query: str, user_context: dict) -> SearchResults:
        """Search documentation with AI enhancement."""
        # First, get traditional search results
        base_results = self.index.search(query)

        if not self.ai.is_available():
            return base_results

        # Enhance with AI understanding
        try:
            ai_interpretation = await self.ai.generate(f"""
            User is searching for: "{query}"
            User's current location in app: {user_context.get('current_page')}
            User's role: {user_context.get('role')}

            From these documentation sections, rank by relevance:
            {[r.title for r in base_results.items[:10]]}

            Also suggest:
            1. Most likely intent (1 sentence)
            2. Related topics they might need
            """, max_tokens=200)

            return SearchResults(
                items=self._rerank(base_results.items, ai_interpretation),
                ai_summary=ai_interpretation.intent,
                related_topics=ai_interpretation.related
            )
        except AIServiceError:
            return base_results
```

---

## 6. Pattern: Skill Progression Tracking

### 6.1 Skill Model

```python
# models/user_skills.py

class UserSkillProfile:
    user_id: str

    # Feature-specific skill levels
    skills: Dict[str, SkillLevel]

    # Achievements and milestones
    achievements: List[Achievement]

    # Learning activity
    tutorials_completed: List[str]
    help_accessed: List[HelpAccess]
    features_used: Dict[str, FeatureUsage]

class SkillLevel(Enum):
    NOVICE = "novice"           # Never used / just started
    BEGINNER = "beginner"       # Used a few times, needs guidance
    INTERMEDIATE = "intermediate"  # Comfortable, occasional help
    ADVANCED = "advanced"       # Proficient, rarely needs help
    EXPERT = "expert"           # Power user, could train others

class FeatureUsage:
    feature_id: str
    first_used: datetime
    last_used: datetime
    usage_count: int
    error_count: int
    help_requests: int
```

### 6.2 Skill Advancement Logic

```python
# services/skill_progression.py

class SkillProgressionService:
    def calculate_skill_level(self, usage: FeatureUsage) -> SkillLevel:
        """Determine skill level based on usage patterns."""
        if usage.usage_count == 0:
            return SkillLevel.NOVICE

        # Error ratio indicates struggling
        error_ratio = usage.error_count / usage.usage_count

        # Help ratio indicates learning
        help_ratio = usage.help_requests / usage.usage_count

        # Recency matters
        days_since_last_use = (datetime.now() - usage.last_used).days

        if usage.usage_count < 5:
            return SkillLevel.BEGINNER
        elif usage.usage_count < 20:
            if error_ratio < 0.1 and help_ratio < 0.1:
                return SkillLevel.INTERMEDIATE
            return SkillLevel.BEGINNER
        elif usage.usage_count < 50:
            if error_ratio < 0.05 and help_ratio < 0.05:
                return SkillLevel.ADVANCED
            return SkillLevel.INTERMEDIATE
        else:
            if error_ratio < 0.02 and help_ratio < 0.02:
                return SkillLevel.EXPERT
            return SkillLevel.ADVANCED

    def suggest_next_learning(self, profile: UserSkillProfile) -> List[str]:
        """Suggest what user should learn next."""
        suggestions = []

        # Find features at beginner level with low usage
        for feature, skill in profile.skills.items():
            if skill == SkillLevel.BEGINNER:
                suggestions.append(f"Practice using {feature}")

        # Find related advanced features to novice skills
        for feature, skill in profile.skills.items():
            if skill == SkillLevel.NOVICE:
                related = self.get_related_features(feature)
                for r in related:
                    if profile.skills.get(r, SkillLevel.NOVICE) >= SkillLevel.INTERMEDIATE:
                        suggestions.append(f"Try {feature} - similar to {r}")

        return suggestions[:3]  # Top 3 suggestions
```

### 6.3 Achievement System

```yaml
# config/achievements.yaml
achievements:
  - id: "first_project"
    name: "Project Pioneer"
    description: "Created your first project"
    trigger:
      type: "feature_first_use"
      feature: "project_create"
    reward: "Unlock project templates"

  - id: "speed_demon"
    name: "Speed Demon"
    description: "Completed 10 tasks in one day"
    trigger:
      type: "count_in_period"
      feature: "task_complete"
      count: 10
      period: "day"
    reward: "Unlock keyboard shortcuts guide"

  - id: "help_graduate"
    name: "Self-Sufficient"
    description: "Used a feature 20 times without accessing help"
    trigger:
      type: "usage_without_help"
      count: 20
    reward: "Advanced features unlocked"
```

---

## 7. Implementation Considerations

### 7.1 Performance

| Concern | Mitigation |
|---------|------------|
| Help content loading | Lazy load, prefetch likely content |
| Skill calculations | Cache, recalculate on significant events |
| AI-enhanced features | Timeout, fallback to non-AI |
| Documentation search | Index in background, incremental updates |

### 7.2 Privacy

| Data Type | Handling |
|-----------|----------|
| Skill progression | Per-user, option to opt out |
| Help access history | Aggregate for analytics, per-user optional |
| Onboarding questions | Store for personalization, user can clear |
| AI interactions | Log for improvement, anonymize for analysis |

### 7.3 Accessibility

- All help content available via keyboard
- Screen reader support for tooltips and popups
- Alternative text for visual tutorials
- Adjustable timing for auto-appearing help

---

## 8. Testing Integrated Training

### 8.1 Test Categories

```python
# tests/test_contextual_help.py

class TestContextualHelp:
    def test_new_user_sees_essential_help(self):
        """New users should see essential-level help."""
        user = create_test_user(skill_level="new")
        help_content = help_service.get_help("registration", "email", user.id)
        assert help_content.level == "essential"

    def test_experienced_user_not_interrupted(self):
        """Experienced users shouldn't see basic help."""
        user = create_test_user(skill_level="advanced")
        user.mark_help_seen("registration", "email", "helpful")
        help_content = help_service.get_help("registration", "email", user.id)
        assert help_content is None

    def test_error_provides_specific_guidance(self):
        """Errors should provide actionable help."""
        help_text = help_service.get_error_help("registration", "invalid_email")
        assert "email" in help_text.lower()
        assert "@" in help_text  # Specific guidance

    def test_help_dismissal_persists(self):
        """Dismissed help should not reappear."""
        user = create_test_user()
        help_service.dismiss_help(user.id, "feature", "context")
        help_content = help_service.get_help("feature", "context", user.id)
        assert help_content is None
```

### 8.2 Onboarding Flow Tests

```python
# tests/test_onboarding.py

class TestOnboarding:
    def test_personalized_path_based_on_goals(self):
        """Learning path should reflect user's stated goals."""
        goals = ["project_management", "reporting"]
        path = onboarding_service.generate_learning_path(goals)
        assert "create_project" in path
        assert "generate_report" in path
        assert "time_tracking" not in path  # Not in goals

    def test_stuck_detection_triggers_help(self):
        """User stuck on step should receive proactive help."""
        user = create_test_user()
        start_onboarding_step(user, "create_project")
        advance_time(minutes=6)
        assert onboarding_service.should_offer_help(user) is True

    def test_completion_tracking_accurate(self):
        """Completion percentage should reflect progress."""
        user = create_test_user()
        user.learning_path = ["step1", "step2", "step3", "step4"]
        complete_steps(user, ["step1", "step2"])
        assert user.completion_percentage() == 50.0
```

---

## 9. Related Documents

- [AOSD Framework - Goal 6](../../../AOSD_FRAMEWORK.md#goal-6--embed-training-and-knowledge-directly-into-applications) - Embedded Training principle
- [AOSD Framework - Goal 5](../../../AOSD_FRAMEWORK.md#goal-5--integrate-ai-into-applications-as-first-class-runtime-capabilities) - AI Runtime Integration
- [Troubleshooting - Integrated AI Features](../../../TROUBLESHOOTING.md#integrated-ai-features-in-applications) - Common issues
- [Security Monitoring Example](./security-monitoring-example.md) - Related integrated feature

---

**End of Integrated Training Reference Implementation**
