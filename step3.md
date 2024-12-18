-------------------------------  copy the  code and paste in your script.sh file  -------------------------------------------------------



```bash 

#!/bin/bash

sleep 3              # so that my laptop get contected to internet on restart 



export DISPLAY=:0
export XDG_SEAT_PATH=/org/freedesktop/DisplayManager/Seat0


# Configuration
BOT_TOKEN="paste_your_telegram_bot_token_here"  # Replace with your actual bot token
CHAT_ID="paste_your_telegram_bot_chat_id_here"      # Replace with your actual chat ID
PHOTO_DIR="/home/user/Desktop/imposter/imposter_images"   # directory where photo of imposter will br saved locally , CHANGE IT  
LOG_FILE="/home/user/Desktop/imposter/telegram_bot_log_file.log"    # change it  ,this log file will hold response and conversation with bot , more detail in next readme file 
RESPONSE_FILE="/home/user/Desktop/imposter/lock_response.txt"     # this is for debugging part , saves the response by you given to telegram bot 




# Generate a random 4-digit number
VERIFICATION_CODE=$(printf "%04d" $((RANDOM % 10000)))



# Create the photo directory if it doesn't exist
mkdir -p "$PHOTO_DIR"



# Capture the photo with a timestamp
TIMESTAMP=$(date "+%Y-%m-%d_%H-%M-%S")
PHOTO_TIMESTAMP=$(date +%s)  # Capture current timestamp in seconds
PHOTO_PATH="$PHOTO_DIR/imposter_$TIMESTAMP.jpg"



# Prepare message with verification code
MESSAGE=$(echo -e "Login attempt detected. Verification Code: $VERIFICATION_CODE\n\nHere's the photo of the person.\n\nReply with:\n- LOCK$VERIFICATION_CODE to lock the system\n- CONTINUE$VERIFICATION_CODE to approve\n- SHUTDOWN$VERIFICATION_CODE to shut down system")




# Capture photo
if fswebcam -r 1280x720 --no-banner "$PHOTO_PATH" >> "$LOG_FILE" 2>&1; then
    echo "$(date) - Photo captured successfully. Sending photo to Telegram..." >> "$LOG_FILE"
    
    # Send the photo via Telegram bot
    if curl -s -X POST "https://api.telegram.org/bot$BOT_TOKEN/sendPhoto" \
         -F "chat_id=$CHAT_ID" \
         -F "photo=@$PHOTO_PATH" \
         -F "caption=$MESSAGE" >> "$LOG_FILE" 2>&1; then
        echo "$(date) - Photo sent to Telegram successfully." >> "$LOG_FILE"
        
    else
        echo "$(date) - Failed to send photo to Telegram." >> "$LOG_FILE"
        exit 1
    fi
else
    echo "$(date) - Failed to capture photo. Image not found at $PHOTO_PATH" >> "$LOG_FILE"
    exit 1
fi




# Wait for a response from Telegram
echo "$(date) - Waiting for Telegram response..." >> "$LOG_FILE"




# Poll for Telegram updates
MAX_ATTEMPTS=60  # Limit polling to 5 minutes (60 attempts * 5 seconds)
ATTEMPT=0



while [ $ATTEMPT -lt $MAX_ATTEMPTS ]; do
     
    # Get updates and use jq to parse JSON more reliably
    RESPONSE=$(curl -s "https://api.telegram.org/bot$BOT_TOKEN/getUpdates" | jq -r '.')
    
    # Extract the latest message details
    LATEST_MESSAGE=$(echo "$RESPONSE" | jq -r '.result[-1].message.text // empty')
    MESSAGE_TIMESTAMP=$(echo "$RESPONSE" | jq -r '.result[-1].message.date // 0')
    
    echo "Debug - Latest Message: $LATEST_MESSAGE" >> "$LOG_FILE"
    echo "Debug - Message Timestamp: $MESSAGE_TIMESTAMP" >> "$LOG_FILE"
    echo "Debug - Photo Timestamp: $PHOTO_TIMESTAMP" >> "$LOG_FILE"
        
   
    
    # Check for lock command with verification code
    if [[ "$LATEST_MESSAGE" == "LOCK$VERIFICATION_CODE" ]]; then
        
        echo "Lock command received. Locking the system..." >> "$LOG_FILE"
        echo "Lock" 
        # Uncomment the line below to actually lock the screen
         dm-tool lock
        echo "$(date) - Lock action taken." >> "$LOG_FILE"
        break
    
    # Check for continue command with verification code    
    elif [[ "$LATEST_MESSAGE" == "CONTINUE$VERIFICATION_CODE" ]]; then
        echo "It's you command received. No action taken." >> "$LOG_FILE"
        echo "It's you" 
        
        break
    
    # Check for shutdown command with verification code    
    elif [[ "$LATEST_MESSAGE" == "SHUTDOWN$VERIFICATION_CODE" ]]; then
        echo "shut down you command received. shutting off the system" >> "$LOG_FILE"
        echo "shutting down" 
       
        # Uncomment the line below to actually shut down
        shutdown -h now
        break
    fi
 
    sleep 5  # Wait 5 seconds before checking again
    ((ATTEMPT++))
done

if [ $ATTEMPT -eq $MAX_ATTEMPTS ]; then
    echo "$(date) - No response received within timeout period." >> "$LOG_FILE"
fi

echo "$(date) - Script finished execution." >> "$LOG_FILE"
exit 0

```