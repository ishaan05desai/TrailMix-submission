# TrailMix On-Screen Meeting Detector

## Overview

The On-Screen Meeting Detector is a feature that automatically detects meetings visible on web pages and creates tasks for them in TrailMix. It works entirely from DOM content without calling external APIs, ensuring privacy and reliability.

## Features

- **Real-time Detection**: Scans pages every 5 seconds for meeting information
- **Multiple Providers**: Supports Google Meet, Zoom, Microsoft Teams, and Webex
- **Smart Time Parsing**: Handles absolute times (e.g., "Today 3:00–3:30 PM") and relative times (e.g., "in 15 minutes")
- **Confidence Scoring**: Uses multiple signals to determine meeting validity
- **Deduplication**: Prevents duplicate tasks from the same meeting
- **Privacy-First**: All processing happens locally in the browser

## How It Works

### 1. Link Detection
The detector scans all visible links on the page and identifies meeting URLs using regex patterns:

- **Google Meet**: `https://meet.google.com/[a-z0-9-]+`
- **Zoom**: `https://zoom.us/j/[0-9]+(?:\?pwd=[A-Za-z0-9]+)?`
- **Microsoft Teams**: `https://teams.microsoft.com/l/meetup-join/[^ \n]+`
- **Webex**: `https://[a-z0-9-]+\.webex\.com/[^\s]+`

### 2. Time Extraction
For each meeting link found, the detector searches nearby text for time information:

**Absolute Time Patterns:**
- `Today 3:00–3:30 PM`
- `3:00 PM – 3:30 PM`
- `Sep 27, 2:15 PM – 3:00 PM`
- `2025-09-27 14:00` (RFC format)

**Relative Time Patterns:**
- `in 10 minutes`
- `starts in 1 hr`
- `starting soon`

### 3. Context Analysis
The detector analyzes surrounding text for additional context:

- **Meeting Keywords**: "Join", "RSVP", "Meeting", "Event", etc.
- **Organizer Information**: "Organizer", "Host", "Created by"
- **Location Details**: "Room", "Location", "Venue"
- **Past Indicators**: "Yesterday", "Last week" (excluded)

### 4. Confidence Scoring
Each detected meeting receives a confidence score:

- **+0.5**: Valid meeting link present
- **+0.2**: Valid future time parsed within window
- **+0.1**: Meeting keywords like "Join" or RSVP controls
- **+0.1**: Organizer/attendees labels found
- **-0.3**: Time text looks like "Yesterday/Last week"

**Threshold**: Only meetings with confidence ≥ 0.6 are processed

### 5. Task Creation
Valid meetings are converted to tasks with:

- **Title**: Event title, organizer name, or provider name
- **Description**: Meeting link, provider, organizer, location
- **Due Date**: Meeting start time
- **Priority**: Medium (configurable)
- **Metadata**: Confidence score, deduplication key, source info

## Configuration

### Meeting Window
Default: **120 minutes** (now → +120 minutes)
- Only meetings starting within this window are detected
- Configurable via `meetingWindowMinutes` property

### Scan Interval
Default: **5 seconds**
- How often the detector scans for new meetings
- Configurable via `scanInterval` property

### Visibility Rules
- Elements must be visible (`offsetParent` not null)
- Must have positive width/height
- Time text must be within 600px vertical distance
- Searches within ±5 sibling nodes

## Supported Platforms

### Gmail
- Calendar invite emails with "Join with Google Meet" buttons
- RSVP controls and meeting details
- Thread lists showing invite blocks

### Outlook
- Mail with Teams "Join" buttons
- Calendar events with meeting links
- Event cards with time ranges

### Google Calendar
- Agenda/day/week views
- Event chips and detail drawers
- Meeting links in event descriptions

### Generic Pages
- Any page with visible meeting links and time information
- Works with custom meeting platforms

## Deduplication

### Client-Side
- Tracks detected meetings using `dedupeKey`
- Prevents reprocessing identical DOM content
- Uses hash of nearby text + link + start time

### Server-Side
- Compares task titles (80% fuzzy match)
- Checks start time within ±5 minutes
- Verifies same meeting link
- Updates existing tasks instead of creating duplicates

## Privacy & Security

- **Local Processing**: All analysis happens in the browser
- **No External APIs**: No calls to Google/Microsoft/other services
- **Minimal Data**: Only normalized meeting info is sent to server
- **No Content Storage**: Raw email bodies/DOM not persisted
- **User Control**: Can be disabled via extension settings

## Testing

### Unit Tests
Run the test suite to verify core functionality:

```bash
npm test extension/meeting-detector.test.js
```

Tests cover:
- Link parsing for all providers
- Time parsing (absolute and relative)
- Confidence scoring
- Deduplication key generation

### Manual Testing Scenarios

1. **Gmail Invite**: Open Gmail with calendar invite → verify task created
2. **Outlook Teams**: Open Outlook with Teams meeting → verify task created
3. **Google Calendar**: Open event drawer → verify task created
4. **Duplicate Prevention**: Same meeting on multiple pages → verify single task
5. **Time Updates**: Change time text → verify task updated
6. **No Time**: Meeting link without time → verify no task created

## Troubleshooting

### Common Issues

**No meetings detected:**
- Check if meeting links are visible on page
- Verify time information is nearby (within 600px)
- Ensure confidence score ≥ 0.6

**Duplicate tasks:**
- Check deduplication key generation
- Verify server-side deduplication logic
- Clear browser cache if needed

**Wrong time parsing:**
- Check time format matches supported patterns
- Verify timezone detection
- Test with different time formats

### Debug Mode

Enable debug logging by opening browser console and looking for:
- `📅 Meeting Detector:` messages
- `🔍 Meeting Detector: Scanning for meetings...`
- `📊 Meeting Detector: Found X meeting candidates`
- `✅ Meeting Detector: Processed X valid meetings`

## API Reference

### MeetingDetector Class

```javascript
const detector = new MeetingDetector();

// Configuration
detector.meetingWindowMinutes = 120; // Meeting window in minutes
detector.scanInterval = 5000; // Scan interval in milliseconds

// Methods
detector.detectMeetings(); // Manual scan
detector.destroy(); // Cleanup
```

### Meeting Object Structure

```javascript
{
  source: "screen-scan",
  title: "📅 Meeting with John Doe",
  start: "2025-01-27T15:00:00.000Z",
  end: "2025-01-27T15:30:00.000Z",
  timezoneGuess: "America/New_York",
  meetingLink: "https://meet.google.com/abc-def-ghi",
  provider: "meet",
  location: "Conference Room A",
  organizer: "John Doe",
  confidence: 0.8,
  rawContextHash: "abc123",
  dedupeKey: "screen-scan::meet::https://meet.google.com/abc-def-ghi::2025-01-27T15:00:00.000Z"
}
```

## Future Enhancements

- **More Providers**: Support for additional meeting platforms
- **Smart Scheduling**: Integration with calendar APIs
- **Meeting Reminders**: Customizable reminder settings
- **Meeting Notes**: Automatic note-taking integration
- **Attendee Detection**: Parse attendee lists from invites
- **Recurring Meetings**: Handle recurring meeting patterns

## Contributing

When adding new meeting providers or time formats:

1. Add regex pattern to `parseMeetingLink()`
2. Add time format to `parseTimeText()`
3. Update confidence scoring if needed
4. Add unit tests for new functionality
5. Update documentation

## License

This feature is part of TrailMix and follows the same license terms.
