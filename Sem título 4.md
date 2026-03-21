from fastapi import APIRouter, WebSocket, WebSocketDisconnect  
from src.infrastructure.services.websocket_manager import notification_manager  
from src.config.logger import Logger  
  
logger = Logger()  
router = APIRouter()  
  
@router.websocket("/ws/notifications")  
async def websocket_notifications(websocket: WebSocket):  
    """  
    WebSocket endpoint for real-time notifications.    Clients connect to this endpoint to receive JSON messages about system events.    """    logger.info(f"WebSocket endpoint hit: {websocket.url} (Manager ID: {id(notification_manager)})")  
    await notification_manager.connect(websocket)  
    try:  
        while True:  
            # Keep connection alive  
            data = await websocket.receive_text()  
            if data == "ping":  
                await websocket.send_text("pong")  
            # We don't expect other messages from client yet  
            logger.debug(f"Received message from WebSocket client: {data}")  
            except WebSocketDisconnect:  
        notification_manager.disconnect(websocket)  
    except Exception as e:  
        logger.error(f"Unexpected error in WebSocket connection: {e}")  
        notification_manager.disconnect(websocket)