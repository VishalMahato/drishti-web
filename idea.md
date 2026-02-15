# Drishti AI  
## JARVIS-like Voice-Controlled Web Browser Agent  

---

## 1. Introduction

Drishti AI is a voice-driven, agentic web browser that acts as your personal assistant for navigating the internet. Think JARVIS, but for web browsing.

You speak naturally, and Drishti understands your intent, navigates websites, fills forms, extracts information, and completes tasks on your behalf - all through voice commands.

Unlike traditional screen readers that passively read content, or browser extensions that require manual clicking, Drishti AI is an intelligent agent that:
- Understands what you want to accomplish
- Figures out how to do it
- Executes the actions autonomously
- Keeps you informed throughout

**Primary Use Case**: Empowering visually impaired users in India, but useful for anyone who wants hands-free web browsing.

**Example Tasks**:
- "Book a train ticket from Delhi to Mumbai"
- "Find me the cheapest wireless mouse under ₹1000"
- "Fill out this government form with my details"
- "Read me the top 3 news headlines"
- "Order my usual from Swiggy"

---

## 2. Problem Statement

### The Web is Still Not Accessible

Despite decades of web development, browsing the internet remains:

1. **Tedious for Everyone**
   - Repetitive form filling
   - Multi-step processes requiring constant clicking
   - Information scattered across multiple pages
   - No memory of your preferences

2. **Especially Difficult for Visually Impaired Users**
   - Screen readers read linearly, don't understand goals
   - Complex interfaces with poor semantic structure
   - Dynamic elements (dropdowns, date pickers) are hard to navigate
   - Multi-page flows require memorization
   - **2 crore+ visually impaired citizens in India** face these barriers daily

3. **Existing Solutions Fall Short**
   - **Screen readers**: Passive, linear reading only
   - **Browser automation tools**: Require programming knowledge
   - **Voice assistants (Alexa, Siri)**: Limited to specific integrations, can't navigate arbitrary websites
   - **Browser extensions**: Still require manual interaction

### What's Missing?

A general-purpose, intelligent agent that can:
- Understand natural voice commands
- Navigate any website autonomously
- Complete multi-step tasks end-to-end
- Learn from your preferences
- Work across all web platforms

---

## 3. Vision

Build a JARVIS-like web browser agent that makes the internet truly accessible and effortless for everyone through natural voice interaction.

**Core Principle**: You describe what you want, Drishti figures out how to do it.

**Long-term Vision**: 
- Universal web accessibility through voice
- Personal AI assistant that knows your preferences
- Hands-free internet for everyone
- Eliminate repetitive digital tasks

**MVP Goal**: 
Build the core agent architecture that can handle general web navigation and task completion, validated through real-world use cases like ticket booking, e-commerce, and form filling.

---

## 4. Proposed Solution

Drishti AI is a voice-controlled browser agent with three core capabilities:

### 1. Universal Web Navigation
- Navigate to any website by voice
- Understand page structure and content
- Extract relevant information
- Scroll, click, and interact with any element

### 2. Task Completion
- Multi-step task execution (booking, shopping, form filling)
- Intelligent decision-making based on page state
- Error detection and recovery
- User confirmation for critical actions

### 3. Personalization & Memory
- Remember your preferences (addresses, payment methods, frequent tasks)
- Learn from your behavior
- Auto-fill forms with stored data
- Suggest actions based on context

**How It Works**:
- You speak naturally: "Book me a train ticket to Mumbai"
- Drishti understands intent and breaks it into steps
- Executes browser actions autonomously (navigate, fill forms, click buttons)
- Keeps you informed with conversational feedback
- Asks for confirmation before critical actions
- Completes the task end-to-end

**MVP Scope**:
- ✅ Voice command parsing (Hindi + English)
- ✅ General web navigation (any website)
- ✅ Form filling automation
- ✅ Information extraction and reading
- ✅ Multi-step task execution
- ✅ User preference storage
- ✅ Conversational feedback
- ⏳ Payment handling (manual handoff initially)
- ⏳ Advanced learning and personalization

---

## 5. System Architecture Overview

Drishti AI follows a hybrid agent architecture:

### 1. Voice Interface Layer
- **Input**: Amazon Transcribe (Speech-to-Text) with Hindi + English support
- **Output**: Amazon Polly (Text-to-Speech) with Indian English voice
- **Fallback**: Text-based interface for testing and accessibility

### 2. AI Planning Layer (The Brain)
- **LLM**: Amazon Bedrock (Claude or Llama)
- **Responsibility**: 
  - Parse user intent from voice input
  - Understand current page context
  - Generate structured task plan
  - Decide next best action
  - Handle ambiguity and errors
  - Reason about multi-step workflows
- **Constraint**: LLM never directly controls browser - only generates structured commands

### 3. Tool Execution Layer (The Hands)
- **Engine**: Playwright (browser automation)
- **Universal Browser Tools**:
  - `navigate(url)` - Go to any website
  - `click(selector)` - Click any element
  - `fill(selector, value)` - Fill form fields
  - `extract_text(selector)` - Extract information
  - `scroll(direction)` - Scroll page
  - `get_page_state()` - Get current page context (DOM structure, visible text)
  - `search_element(description)` - Find elements by natural description
  - `wait_for(condition)` - Wait for page changes
  - `take_screenshot()` - Capture current state
  - `handle_captcha()` - Manual intervention trigger

### 4. Memory Layer
- **Storage**: Local encrypted JSON + Vector DB (for semantic search)
- **Stores**:
  - User profile (name, contact, addresses)
  - Preferences (payment methods, delivery addresses, frequent sites)
  - Task history (for learning patterns)
  - Website-specific data (login credentials, saved forms)
- **Consent**: Explicit opt-in with granular control

### 5. Infrastructure Layer
- **MVP**: Local Python application with Playwright
- **Future**: 
  - Cloud deployment (AWS EC2/Lambda)
  - Browser extension version
  - Mobile app
  - Multi-user support

---

## 6. Agent Execution Flow

**Example: "Book me a train ticket from Delhi to Mumbai on 25th March"**

1. **Voice Input** → Amazon Transcribe → Text: "Book me a train ticket from Delhi to Mumbai on 25th March"

2. **LLM Planning** (Bedrock):
   - Intent: Book train ticket
   - Extract parameters: from="Delhi", to="Mumbai", date="25 March"
   - Missing info: class preference, passenger details
   - Generate plan: [navigate_to_irctc, search_trains, select_train, fill_passenger, review, confirm]

3. **Tool Execution** (Playwright):
   - Execute: `navigate("https://www.irctc.co.in")`
   - Execute: `get_page_state()` → Returns page structure

4. **LLM Decision Loop**:
   - Analyze page state: Login required
   - Decision: Ask user or use stored credentials
   - Action: "I need to log in to IRCTC. Should I use your saved credentials?"

5. **User Response** → "Yes, use saved credentials"

6. **Continue Execution**:
   - Fill login form with stored data
   - Navigate to booking page
   - Fill search form (from, to, date)
   - Extract search results
   - Present options to user

7. **Voice Output** → Amazon Polly → "Found 15 trains. Rajdhani Express departs at 4:55 PM, arrives 8:35 AM. Fare ₹2,500. Should I book this?"

8. **User Confirmation** → Loop continues until task complete

**Key Principles**:
- LLM plans and reasons, Playwright executes
- Continuous feedback loop between page state and LLM
- User confirmation for critical actions
- Graceful error handling and recovery

---

## 7. Core Features

### Must-Have (MVP)
- ✅ Voice command interface (Hindi + English)
- ✅ Universal web navigation (any website)
- ✅ Intelligent page understanding (DOM analysis + LLM reasoning)
- ✅ Form filling automation
- ✅ Multi-step task execution
- ✅ Information extraction and reading
- ✅ User preference storage (encrypted)
- ✅ Conversational feedback at each step
- ✅ Error detection and recovery
- ✅ User confirmation for critical actions

### Validated Use Cases (MVP)
- ✅ Train ticket booking (IRCTC)
- ✅ E-commerce shopping (Amazon, Flipkart)
- ✅ Government form filling
- ✅ Web search and information extraction
- ✅ Food ordering (Swiggy, Zomato)

### Post-MVP Enhancements
- ⏳ Payment integration (secure handling)
- ⏳ Advanced learning (user behavior patterns)
- ⏳ Proactive suggestions ("You usually order lunch at this time")
- ⏳ Multi-tab management
- ⏳ Screenshot analysis for visual elements

### Future Expansion
- 🔮 Browser extension version
- 🔮 Mobile app
- 🔮 Regional language expansion
- 🔮 Integration with other assistants (Alexa, Google Assistant)
- 🔮 Collaborative browsing (share sessions)
- 🔮 API for developers to build custom workflows

---

## 8. Unique Selling Proposition (USP)

1. **Universal Agent**: Works on ANY website, not limited to specific integrations
2. **Hybrid Intelligence**: LLM reasoning + deterministic browser automation = smart + reliable
3. **Goal-Oriented**: You say what you want, Drishti figures out how to do it
4. **Voice-First**: Natural conversation in Hindi + English (code-switching supported)
5. **True Autonomy**: Completes multi-step tasks end-to-end without constant hand-holding
6. **Built for Bharat**: Designed for Indian users, Indian websites, Indian languages
7. **Privacy-First**: Local storage, encrypted data, user control

---

## 9. Example Use Cases

### Use Case 1: Train Ticket Booking

**User**: "Book me a train ticket from Delhi to Mumbai on 25th March"

**Drishti**: "Sure! Opening IRCTC... Which class would you prefer?"

**User**: "AC 3-tier"

**Drishti**: "Searching trains... Found 15 options. Rajdhani Express departs 4:55 PM, arrives 8:35 AM. Fare ₹2,500. Should I book this?"

**User**: "Yes"

**Drishti**: "Filling passenger details from your profile... Name: [name], Age: [age]. Berth preference: Lower. Correct?"

**User**: "Yes"

**Drishti**: "Reviewing booking... Train 12951, 25 March, AC 3-tier, ₹2,500. Confirm?"

**User**: "Confirm"

**Drishti**: "Proceeding to payment. Please complete payment manually."

---

### Use Case 2: E-commerce Shopping

**User**: "Find me a wireless mouse under ₹1000"

**Drishti**: "Searching on Amazon... Found 47 results. Top option: Logitech M331, ₹799, 4.3 stars, 12,000 reviews. Want to see more or add this to cart?"

**User**: "Show me 3 more options"

**Drishti**: "Option 2: HP X3000, ₹599, 4.1 stars. Option 3: Dell MS116, ₹449, 4.0 stars. Option 4: Lenovo 300, ₹699, 4.2 stars. Which one?"

**User**: "Add the Logitech one to cart"

**Drishti**: "Added to cart. Proceed to checkout?"

---

### Use Case 3: Information Extraction

**User**: "What are today's top news headlines?"

**Drishti**: "Opening Times of India... Top 3 headlines: 1) [headline 1], 2) [headline 2], 3) [headline 3]. Want me to read any article?"

**User**: "Read the first one"

**Drishti**: *[Reads article content]*

---

### Use Case 4: Form Filling

**User**: "Fill out this government scholarship form"

**Drishti**: "I see a scholarship application form. I'll use your saved details. Name: [name], Date of Birth: [dob], Address: [address]. Should I proceed?"

**User**: "Yes"

**Drishti**: "Filling form... Done. Review before submission?"

**User**: "Yes, read it back to me"

**Drishti**: *[Reads filled form]* "Everything correct?"

**User**: "Submit"

**Drishti**: "Form submitted successfully. Reference number: [ref_no]"

---

## 10. Risk & Mitigation

### Risk: LLM Hallucination
**Impact**: High - Could execute wrong actions or misinterpret intent  
**Mitigation**:
- Structured tool calls with validation
- User confirmation before every critical action
- Read-back of extracted information for user verification
- Confidence scoring for LLM decisions
- Fallback to asking user when uncertain

### Risk: Website Layout Changes
**Impact**: High - Selectors break, automation fails  
**Mitigation**:
- Use semantic selectors (aria-labels, data attributes, role attributes)
- Implement selector fallback chains (ID → class → text content → position)
- LLM-based element discovery as last resort
- Graceful degradation: Alert user if automation fails, provide manual guidance
- Community-driven selector updates (crowdsourced fixes)

### Risk: Captcha Handling
**Impact**: High - Blocks automation flow  
**Mitigation**:
- Detect captcha presence
- Trigger manual intervention: "Captcha detected. Please solve it and say 'done'"
- Resume automation after user confirmation
- Explore captcha-solving services (with user consent)

### Risk: Session Timeouts
**Impact**: Medium - User has to restart task  
**Mitigation**:
- Keep session alive with periodic pings
- Detect timeout and alert user immediately
- Save task state to resume quickly
- Auto-retry with saved credentials

### Risk: Voice Recognition Errors
**Impact**: Medium - Wrong parameters extracted  
**Mitigation**:
- Confirm extracted information: "You said Delhi to Mumbai on 25th March. Is that correct?"
- Allow corrections: "No, I said 26th March"
- Fuzzy matching for common terms (station names, product names)
- Support spelling out critical information

### Risk: User Trust & Adoption
**Impact**: Critical - Users won't adopt if they don't trust the system  
**Mitigation**:
- Transparent communication at every step
- Never proceed without explicit confirmation for critical actions
- Provide "undo" or "cancel" options at any point
- User testing with visually impaired community before launch
- Clear privacy policy and data handling practices

### Risk: Security & Privacy
**Impact**: Critical - Sensitive data exposure  
**Mitigation**:
- Local encrypted storage (AES-256)
- No cloud sync without explicit consent
- Secure credential management
- Regular security audits
- Open-source core components for transparency

---

## 11. Security & Privacy

### Data Storage
- **What's Stored**: Name, contact, addresses, preferences, task history
- **What's NOT Stored**: Payment details (unless explicitly opted in), browsing history outside tasks
- **Encryption**: AES-256 encryption for local storage
- **Access**: User-only access, no cloud sync in MVP

### Voice Data
- **Processing**: Voice sent to Amazon Transcribe, transcribed to text
- **Retention**: No raw audio stored after transcription
- **Privacy**: AWS Transcribe privacy policy applies
- **Future**: Explore local speech-to-text for complete privacy

### Authentication & Credentials
- **MVP**: Local machine access only (no multi-user support)
- **Credential Storage**: Encrypted with master password
- **Access Control**: User must authenticate before accessing stored credentials
- **Future**: Biometric authentication, hardware security keys

### Consent & Control
- **First Run**: Explicit consent for data storage with granular options
- **Transparency**: User can view, edit, or delete stored data anytime
- **Commands**: "Show my saved details", "Delete my data", "What do you know about me?"
- **Audit Log**: Track all actions taken by the agent

### Payment Security
- **MVP Approach**: Manual handoff to user for payment
- **Rationale**: Avoid handling sensitive payment data in MVP
- **Future**: Explore secure payment integration with tokenization and user consent

---

## 12. MVP Development Plan

### Phase 1: Core Agent Engine (Weeks 1-3)
- Set up Playwright browser automation framework
- Implement universal browser tools (navigate, click, fill, extract)
- Build page state analyzer (DOM → structured data)
- Test on 3-5 different websites (IRCTC, Amazon, government portal)

### Phase 2: LLM Integration (Weeks 4-5)
- Integrate Amazon Bedrock for intent parsing and planning
- Implement agent decision loop (LLM → Tool → Feedback → LLM)
- Build structured tool calling system
- Test with text-based commands first

### Phase 3: Voice Interface (Week 6)
- Integrate Amazon Transcribe (Hindi + English)
- Integrate Amazon Polly for conversational responses
- Test voice input/output flow
- Handle code-switching and accent variations

### Phase 4: Memory & Personalization (Week 7)
- Build encrypted local storage for user preferences
- Implement preference auto-fill system
- Add consent and data management UI
- Test with stored user data

### Phase 5: Error Handling & Polish (Week 8)
- Implement robust error detection and recovery
- Add user confirmation loops for critical actions
- Build graceful degradation for unsupported scenarios
- Test edge cases across multiple websites

### Phase 6: User Testing (Weeks 9-10)
- Recruit 10-15 visually impaired users for testing
- Conduct supervised testing sessions with real tasks
- Gather feedback on usability, trust, and effectiveness
- Iterate based on user feedback

### Success Criteria
- ✅ 80% task completion rate across 5 different use cases
- ✅ Average task time < 5 minutes
- ✅ User satisfaction score > 4/5
- ✅ Zero critical errors (wrong actions, data loss)
- ✅ Works on at least 10 different Indian websites

---

## 13. Post-MVP Roadmap

### Phase 2: Enhanced Capabilities
- Payment integration (secure, with user consent)
- Advanced learning (behavior patterns, proactive suggestions)
- Multi-tab management
- Screenshot analysis for visual elements
- Faster execution with caching

### Phase 3: Platform Expansion
- Browser extension (Chrome, Firefox)
- Mobile app (Android first, then iOS)
- Desktop application (Windows, Mac, Linux)
- Integration with existing assistive tech

### Phase 4: Language & Reach
- Regional language support (Tamil, Bengali, Marathi, Telugu)
- Improved accent handling
- Offline mode for basic tasks
- Rural accessibility (low bandwidth optimization)

### Phase 5: Ecosystem & Partnerships
- API for developers to build custom workflows
- Integration with voice assistants (Alexa, Google Assistant)
- Partner with National Association for the Blind (NAB)
- Government collaboration for Digital India initiative
- Enterprise version for organizations

---

## 14. MVP Cost Estimate

### Development (10 weeks)
- Developer time: 1-2 full-time developers
- User testing: ₹30,000 (participant compensation + logistics)

### Infrastructure (Monthly - MVP)
- **Amazon Transcribe**: ~₹1,000/month (200 tasks, 5 min each)
- **Amazon Polly**: ~₹400/month (text-to-speech)
- **Amazon Bedrock**: ~₹2,000/month (Claude API calls, ~500 tasks)
- **Local Development**: ₹0 (runs on local machine)
- **Total**: ~₹3,400/month for MVP testing

### Post-MVP (Production - 1000 users)
- AWS EC2 instance: ~₹5,000/month
- S3 storage: ~₹200/month
- Increased API usage: ~₹15,000/month
- **Total**: ~₹20,000/month for 1000 active users

**Note**: Costs scale with usage. AWS Free Tier covers initial development and testing.

---

## 15. Expected Impact

### Primary Impact: Accessibility
- **Target Users**: 2 crore+ visually impaired citizens in India
- **Independence**: Complete online tasks without human assistance
- **Privacy**: No need to share personal info with helpers
- **Empowerment**: Full participation in digital economy

### Secondary Impact: Convenience for Everyone
- **Hands-free browsing**: Useful while cooking, driving, exercising
- **Time savings**: Automate repetitive tasks
- **Reduced friction**: No more tedious form filling
- **Elderly users**: Easier internet access for seniors

### Measurable Outcomes
- Task completion rate across different websites
- Time to complete common tasks
- User satisfaction and trust scores
- Adoption rate among target users
- Reduction in dependency on human assistance

### Long-Term Vision
- **Scale**: 10 lakh+ users across India
- **Domains**: Universal web accessibility
- **Economic**: Enable independent digital participation
- **Social**: Reduce barriers, increase autonomy

---

## 16. Conclusion

Drishti AI is a JARVIS-like voice-controlled web browser agent that makes the internet truly accessible through natural conversation.

Unlike screen readers that passively read content, or voice assistants limited to specific integrations, Drishti is a general-purpose agent that can navigate any website, complete any task, and learn your preferences.

The MVP validates the core architecture through real-world use cases: ticket booking, e-commerce, form filling, and information extraction. Success here proves the technical feasibility and user value.

This is not just an accessibility tool - it's a new way to interact with the web. Voice-first, intelligent, autonomous, and built for everyone.

---

## 17. Next Steps

1. **User Research** (Week 1-2)
   - Interview 15-20 visually impaired users about web browsing challenges
   - Identify most painful and frequent tasks
   - Validate demand and willingness to adopt

2. **Technical Proof** (Week 3-4)
   - Build basic Playwright automation for 3 different websites
   - Test LLM's ability to parse intent and generate plans
   - Validate hybrid architecture feasibility

3. **Prototype** (Week 5-8)
   - Build text-based MVP (no voice yet)
   - Test with 5 different use cases
   - Measure success rate and identify failure modes

4. **Voice Integration** (Week 9-10)
   - Add voice input/output
   - Test with real users
   - Iterate based on feedback

5. **Decision Point**
   - Proceed with full MVP only after validating:
     - User need (research)
     - Technical feasibility (prototype)
     - Task completion rate > 70%

**Goal**: Build something people actually want and that actually works, before scaling.
