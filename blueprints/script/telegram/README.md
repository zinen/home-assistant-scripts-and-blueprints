[![Open your Home Assistant instance and show your scripts.](https://my.home-assistant.io/badges/scripts.svg)](https://my.home-assistant.io/redirect/scripts/)

# telegram_confirm_notify_v5.yaml

Copy script from file telegram_confirm_notify_v5.yaml in this folder

Usage inside automation
```yaml
  - action: script.telegram_confirm_notify_v5
    metadata: {}
    data:
      message: Event happend that needs your approval.
      button1: Approve
      button2: Reject
      timeout: 10
    response_variable: telegram_result
  - choose:
      - conditions:
          - condition: template
            value_template: >-
              {{ telegram_result | default(none) is not none and
              telegram_result.answer == "button1" }}
            alias: if button1
        sequence: []
      - conditions:
          - condition: template
            value_template: >-
              {{ telegram_result | default(none) is not none and
              telegram_result.answer == "button2" }}
            alias: if button2
        sequence: []
      - conditions:
          - condition: template
            value_template: >-
              {{ telegram_result | default(none) is not none and
              telegram_result.answer == "button3" }}
            alias: if button3
        sequence: []
      - conditions:
          - condition: template
            value_template: >-
              {{ telegram_result | default(none) is not none and
              telegram_result.answer == "timeout" }}
            alias: if timeout
        sequence: []
    default:
      - stop: Telegram confirmation unhandled error. Stopping now
        error: true

```