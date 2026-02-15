# Requirements Document

## Introduction

Drishti AI is a voice-driven web automation agent that enables visually impaired users in India to independently book train tickets on IRCTC through natural voice commands in Hindi and English. The system uses a hybrid architecture combining voice interfaces (Amazon Transcribe and Polly), AI planning (Amazon Bedrock LLM), browser automation (Playwright), and local encrypted memory for user preferences.

This MVP focuses on validating the core architecture with train ticket booking - a high-value, complex use case that demonstrates technical feasibility and provides immediate independence for a critical life task.

## Glossary

- **Voice_Interface**: The speech-to-text and text-to-speech components using Amazon Transcribe and Polly
- **AI_Planner**: The LLM-based component (Amazon Bedrock) that parses user intent and generates task plans
- **Automation_Engine**: The Playwright-based browser automation component that executes actions on IRCTC
- **Memory_Store**: Local encrypted JSON storage for user preferences and passenger details
- **IRCTC**: Indian Railway Catering and Tourism Corporation website (irctc.co.in)
- **User**: A visually impaired person using Drishti AI to book train tickets
- **Booking_Session**: A complete interaction from initial voice command to booking confirmation
- **Tool**: A specific browser automation function (e.g., search_trains, select_train)
- **Page_State**: The current state of the IRCTC webpage including available options and form fields
- **Code_Switching**: Mixing Hindi and English in the same utterance (e.g., "Delhi se Mumbai ki ticket book karo")

## Requirements

### Requirement 1: Voice Input Processing

**User Story:** As a visually impaired user, I want to speak naturally in Hindi and English, so that I can communicate my booking intent without typing.

#### Acceptance Criteria

1. WHEN a user speaks a voice command, THE Voice_Interface SHALL capture the audio and convert it to text using Amazon Transcribe
2. WHEN the voice input contains Hindi and English words (code-switching), THE Voice_Interface SHALL accurately transcribe both languages
3. WHEN the transcription is complete, THE Voice_Interface SHALL pass the text to the AI_Planner for intent parsing
4. WHEN the audio quality is poor or unclear, THE Voice_Interface SHALL request the user to repeat the command
5. THE Voice_Interface SHALL support continuous listening mode during a Booking_Session

### Requirement 2: Intent Parsing and Task Planning

**User Story:** As a user, I want the system to understand my booking intent from natural speech, so that I don't need to follow rigid command structures.

#### Acceptance Criteria

1. WHEN the AI_Planner receives transcribed text, THE AI_Planner SHALL extract booking parameters (source station, destination station, travel date, class)
2. WHEN booking parameters are ambiguous or missing, THE AI_Planner SHALL generate clarifying questions for the user
3. WHEN all required parameters are extracted, THE AI_Planner SHALL generate a structured task plan with ordered steps
4. WHEN station names are spoken in colloquial form, THE AI_Planner SHALL map them to official IRCTC station codes
5. THE AI_Planner SHALL validate that the travel date is in the future and within IRCTC booking window

### Requirement 3: Train Search Automation

**User Story:** As a user, I want the system to search for trains automatically, so that I can find available options without navigating the IRCTC interface.

#### Acceptance Criteria

1. WHEN the AI_Planner initiates train search, THE Automation_Engine SHALL navigate to the IRCTC homepage
2. WHEN on the IRCTC homepage, THE Automation_Engine SHALL fill the source station, destination station, travel date, and class fields
3. WHEN all search fields are filled, THE Automation_Engine SHALL submit the search form
4. WHEN the search results page loads, THE Automation_Engine SHALL extract the list of available trains with train numbers, names, departure times, arrival times, and seat availability
5. WHEN no trains are available for the selected criteria, THE Automation_Engine SHALL report this to the AI_Planner
6. IF the IRCTC session times out, THEN THE Automation_Engine SHALL detect the timeout and restart the search

### Requirement 4: Train Selection with User Confirmation

**User Story:** As a user, I want to hear available train options and confirm my selection, so that I can make an informed choice.

#### Acceptance Criteria

1. WHEN train search results are available, THE AI_Planner SHALL generate a natural language summary of available trains
2. WHEN multiple trains are available, THE AI_Planner SHALL recommend a train based on departure time and availability
3. WHEN the recommendation is presented, THE Voice_Interface SHALL speak the train details to the user
4. WHEN the user confirms a train selection, THE AI_Planner SHALL instruct the Automation_Engine to select that train
5. WHEN the user requests alternative options, THE AI_Planner SHALL present the next available train
6. THE AI_Planner SHALL require explicit user confirmation before proceeding to passenger details

### Requirement 5: Passenger Detail Management

**User Story:** As a user, I want my passenger details stored securely and auto-filled, so that I don't need to dictate them for every booking.

#### Acceptance Criteria

1. WHEN a user first uses the system, THE Memory_Store SHALL prompt for passenger details (name, age, gender, berth preference)
2. WHEN passenger details are provided, THE Memory_Store SHALL encrypt and store them locally using AES-256 encryption
3. WHEN a train is selected, THE Automation_Engine SHALL retrieve passenger details from the Memory_Store
4. WHEN passenger details are retrieved, THE Automation_Engine SHALL auto-fill the passenger form on IRCTC
5. WHEN the user requests to update stored details, THE Memory_Store SHALL allow modification and re-encrypt the data
6. THE Memory_Store SHALL support only single passenger details for MVP

### Requirement 6: Conversational Feedback

**User Story:** As a visually impaired user, I want to hear what the system is doing at each step, so that I understand the booking progress.

#### Acceptance Criteria

1. WHEN the Automation_Engine executes a Tool, THE AI_Planner SHALL generate a status message describing the action
2. WHEN a status message is generated, THE Voice_Interface SHALL convert it to speech using Amazon Polly
3. WHEN the speech is ready, THE Voice_Interface SHALL play the audio to the user
4. WHEN a critical action is about to be performed, THE AI_Planner SHALL request explicit user confirmation before proceeding
5. THE Voice_Interface SHALL use natural, conversational language in Hindi and English

### Requirement 7: Booking Review and Confirmation

**User Story:** As a user, I want to review all booking details before final submission, so that I can verify everything is correct.

#### Acceptance Criteria

1. WHEN passenger details are filled, THE Automation_Engine SHALL extract all booking details from the IRCTC review page
2. WHEN booking details are extracted, THE AI_Planner SHALL generate a comprehensive summary including train details, passenger details, date, class, and fare
3. WHEN the summary is ready, THE Voice_Interface SHALL read the complete booking details to the user
4. WHEN the user confirms the booking, THE Automation_Engine SHALL proceed to the payment handoff
5. WHEN the user identifies an error, THE AI_Planner SHALL allow the user to go back and modify details
6. THE AI_Planner SHALL require explicit "confirm booking" command before proceeding to payment

### Requirement 8: Error Detection and Recovery

**User Story:** As a user, I want the system to detect errors and guide me through recovery, so that I can complete my booking even when issues occur.

#### Acceptance Criteria

1. WHEN the Automation_Engine encounters an error on IRCTC, THE Automation_Engine SHALL detect the error message or state
2. WHEN an error is detected, THE AI_Planner SHALL analyze the error and determine if recovery is possible
3. WHEN recovery is possible, THE AI_Planner SHALL guide the user through corrective actions
4. WHEN a station name is not found, THE AI_Planner SHALL ask the user to provide an alternative name or spelling
5. WHEN a captcha is detected, THE Automation_Engine SHALL alert the user and pause for manual intervention
6. WHEN the user says "done" after manual intervention, THE Automation_Engine SHALL resume automation
7. IF the IRCTC page layout has changed and selectors fail, THEN THE Automation_Engine SHALL report the failure and provide manual guidance

### Requirement 9: Payment Handoff

**User Story:** As a user, I want to complete payment manually after booking review, so that my payment details remain secure.

#### Acceptance Criteria

1. WHEN the user confirms the booking, THE Automation_Engine SHALL navigate to the payment page
2. WHEN the payment page loads, THE AI_Planner SHALL inform the user that manual payment is required
3. WHEN manual payment is required, THE Voice_Interface SHALL provide instructions for completing payment
4. THE Automation_Engine SHALL not store or process any payment information
5. THE Automation_Engine SHALL pause automation at the payment page and wait for user completion

### Requirement 10: Session Management

**User Story:** As a user, I want the system to maintain my booking session, so that I don't lose progress due to timeouts.

#### Acceptance Criteria

1. WHEN a Booking_Session starts, THE Automation_Engine SHALL maintain the IRCTC session with periodic activity
2. WHEN the IRCTC session is about to expire, THE Automation_Engine SHALL detect the warning and alert the user
3. WHEN a session timeout occurs, THE Automation_Engine SHALL detect the timeout and inform the user
4. WHEN a timeout is detected, THE AI_Planner SHALL offer to restart the booking with saved parameters
5. THE Automation_Engine SHALL save the current booking state after each completed step

### Requirement 11: Data Privacy and Security

**User Story:** As a user, I want my personal data stored securely with my consent, so that my privacy is protected.

#### Acceptance Criteria

1. WHEN a user first launches the system, THE Memory_Store SHALL request explicit consent for data storage
2. WHEN consent is granted, THE Memory_Store SHALL store passenger details with AES-256 encryption
3. WHEN the user requests to view stored data, THE Memory_Store SHALL decrypt and display the information
4. WHEN the user requests to delete data, THE Memory_Store SHALL permanently remove all stored information
5. THE Voice_Interface SHALL not store raw audio after transcription is complete
6. THE Memory_Store SHALL store data only on the local machine with no cloud synchronization in MVP

### Requirement 12: Voice Output Quality

**User Story:** As a visually impaired user, I want clear and natural voice responses, so that I can easily understand the system's feedback.

#### Acceptance Criteria

1. WHEN generating speech output, THE Voice_Interface SHALL use Amazon Polly with Indian English voice
2. WHEN speaking train times, THE Voice_Interface SHALL use 12-hour format with AM/PM
3. WHEN speaking dates, THE Voice_Interface SHALL use natural date format (e.g., "25th March")
4. WHEN speaking prices, THE Voice_Interface SHALL use Indian numbering system (e.g., "rupees one thousand two hundred")
5. THE Voice_Interface SHALL maintain consistent speaking rate and volume throughout the session

### Requirement 13: Graceful Degradation

**User Story:** As a user, I want the system to handle unexpected situations gracefully, so that I'm never left without guidance.

#### Acceptance Criteria

1. WHEN the Automation_Engine cannot locate a page element, THE Automation_Engine SHALL try fallback selectors before failing
2. WHEN all automation attempts fail, THE AI_Planner SHALL provide manual instructions to the user
3. WHEN the AI_Planner cannot parse user intent, THE AI_Planner SHALL ask clarifying questions rather than guessing
4. WHEN network connectivity is lost, THE Automation_Engine SHALL detect the issue and inform the user
5. THE AI_Planner SHALL never proceed with uncertain information without user confirmation

### Requirement 14: Booking Scope Constraints

**User Story:** As a product owner, I want the MVP to focus on single passenger regular bookings, so that we validate the core architecture before expanding scope.

#### Acceptance Criteria

1. THE Automation_Engine SHALL support only single passenger bookings in MVP
2. THE Automation_Engine SHALL support only regular train bookings (not Tatkal) in MVP
3. WHEN a user requests multiple passengers, THE AI_Planner SHALL inform them this feature is not yet available
4. WHEN a user requests Tatkal booking, THE AI_Planner SHALL inform them this feature is not yet available
5. THE AI_Planner SHALL clearly communicate MVP limitations during the first booking session
