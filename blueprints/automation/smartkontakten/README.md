Smartkontakten integration HA

Used to make ABB lookalike 4 button device by Smartkontakten available in home assistant.

1: Buy a 4 button solution from https://www.smartkontakten.dk/
2: Integrate it with Philips Hue Bridge
3: Setup hue integration in home assistant
4: Inside home assistant look for automatons and make a new one from this blue print


Map each button you want to listen to in the correct order as you see them. Hue should named them with numbers from 1-4:
- top-left = 1 
- top-right = 3
- bottom-left = 2 
- bottom-right = 4

There are three actions placeholders. Short press, long press and long press release.

For a one click dimmer solution you need to use a helper for know that a dimmer action is going on. I use it like this:
- Short press, look for helper to already be defined
    - (First parallel run will get here) If no helper, then start a repeat action until helper is false(This will keep going) Make sure to make a catch to prevent run-forever-loop
    - (Second parallel run will get here) If helper is true then mark the it as false

# Debug 

## Listen for events:
http://homeassistent.local:8123/config/tools/event
Subscribe to event: state_changed
Filter old devices: event.friends_of_hue_switch_
Filter new devices: event.works_with_hue_switch_

### Basic event output:
```yaml
event_type: state_changed
data:
  entity_id: event.works_with_hue_switch_1_button_1
  old_state:
    entity_id: event.works_with_hue_switch_1_button_1
    state: '2026-10-04T18:20:09.654+00:00'
    attributes:
      event_types:
        - initial_press
        - repeat
        - short_release
        - long_press
        - long_release
      event_type: short_release
      device_class: button
      friendly_name: Kitchen contact mobil Button 1
    last_changed: '2026-10-17T04:00:29.938411+00:00'
    last_reported: '2026-10-17T04:00:29.938411+00:00'
    last_updated: '2026-10-17T04:00:29.938411+00:00'
    context:
      id: 01M5409CQJHP2464AZD3NA1XF6
      parent_id: null
      user_id: null
  new_state:
    entity_id: event.works_with_hue_switch_1_button_1
    state: '2026-10-17T12:04:02.719+00:00'
    attributes:
      event_types:
        - initial_press
        - repeat
        - short_release
        - long_press
        - long_release
      event_type: initial_press
      device_class: button
      friendly_name: Kitchen contact mobil Button 1
    last_changed: '2026-10-17T12:04:02.719276+00:00'
    last_reported: '2026-10-17T12:04:02.719276+00:00'
    last_updated: '2026-10-17T12:04:02.719276+00:00'
    context:
      id: 01M10VYSGZBSW0R478CRM736NV
      parent_id: null
      user_id: null
origin: LOCAL
time_fired: '2026-10-17T12:04:02.719276+00:00'
context:
  id: 01M10VYSGZBSW0R478CRM736NV
  parent_id: null
  user_id: null

```

Seen event types in data.new_state.event_type:
- initial_press
- short_release
- repeat
- long_release

(Haven't seen the long_press event yet)

Logic for normal press:
`if (data.new_state.event_type == short_release and data.old_state.event_type == initial_press )`

Logic for hold down:
`if (data.new_state.event_type == repeat and data.new_state.old_state in ['initial_press','repeat'] )`

Logic for long press let go:
`if (data.new_state.event_type == long_release and data.new_state.old_state in ['short_release','repeat'] )`

Button locations as seen from trigger.event.data.button:
- 1 = top-left
- 2 = bottom-left
- 3 = top-right
- 4 = bottom-right