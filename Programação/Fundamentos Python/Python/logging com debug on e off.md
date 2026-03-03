```python
import inspect  
import logging  
import threading  
from typing import Dict, Optional  
  
  
class StdLogger:  
    _instance: Optional['StdLogger'] = None  
    _lock = threading.Lock()  
  
    def __new__(cls) -> 'StdLogger':  
        """Thread-safe implementation of Singleton pattern."""  
        if cls._instance is None:  
            with cls._lock:  
                if cls._instance is None:  
                    cls._instance = super().__new__(cls)  
        return cls._instance  
  
    def __init__(self):  
        """Initialize the logger only once."""  
        if hasattr(self, '_initialized'):  
            return  
  
        self._initialized = True  
  
        # Create a unique logger to avoid conflicts with external configuration  
        self._logger = logging.getLogger(f'custom_logger_{id(self)}')  
  
        # Define level based on environment and DEBUG variable using Settings  
        try:  
            from src.config.settings import settings  
            env = settings.ENV.lower()  
            debug_enabled = settings.DEBUG  
        except Exception:  
            # Fallback to environment variables if Settings is not available  
            import os  
            env = os.getenv('ENV', 'production').lower()  
            debug_enabled = os.getenv('DEBUG', 'false').lower() in ('true', '1', 'yes', 'on')  
  
        # Priority: DEBUG setting > ENV setting  
        if debug_enabled:  
            # If DEBUG=True, always use DEBUG level regardless of ENV  
            default_level = logging.DEBUG  
        else:  
            # If DEBUG=False, use level based on ENV  
            if env == 'development':  
                default_level = logging.INFO  # Changed from DEBUG to INFO when DEBUG=False  
            elif env == 'testing':  
                default_level = logging.WARNING  
            else:  # production or any other  
                default_level = logging.INFO  
  
        self._logger.setLevel(default_level)  
  
        self._logger.propagate = False  
  
        for handler in self._logger.handlers[:]:  
            self._logger.removeHandler(handler)  
  
        console_handler = logging.StreamHandler()  
        console_handler.setLevel(default_level)  
        class CustomFormatter(logging.Formatter):  
            def format(self, record):  
                if hasattr(record, 'pathname'):  
                    record.filename = record.pathname  
                return super().format(record)  
  
        formatter = CustomFormatter(            '[%(asctime)s] [%(levelname)s] [%(filename)s:%(lineno)d] [%(funcName)s] %(context)s %(message)s',  
            datefmt='%Y-%m-%d %H:%M:%S'  
        )  
        console_handler.setFormatter(formatter)        self._logger.addHandler(console_handler)  
  
    @classmethod  
    def get_instance(cls) -> 'StdLogger':  
        """Returns the singleton instance of the logger."""  
        return cls()  
  
    def _get_caller_info(self) -> Dict[str, str]:  
        """  
        Automatically captures caller information.  
        Returns:            Dict containing filename, lineno, class_name and function_name        """        # Get the caller frame (skip this method and the log method)  
        frame = inspect.currentframe()  
        try:  
            # Go up 3 levels: _get_caller_info -> _log -> public method (debug/info/etc)  
            caller_frame = frame.f_back.f_back.f_back  
  
            if caller_frame is None:  
                return {  
                    'filename': 'unknown',  
                    'lineno': '0',  
                    'class_name': 'Unknown',  
                    'function_name': 'unknown'  
                }  
  
            # Use relative path from 'src'  
            full_path = caller_frame.f_code.co_filename  
  
            # Find 'src' position in path and get from there  
            if 'src' in full_path:  
                src_index = full_path.find('src')  
                filename = full_path[src_index:].replace('/', '\\')  
            else:  
                # If 'src' not found, use only the filename  
                import os  
                filename = os.path.basename(full_path)  
            lineno = str(caller_frame.f_lineno)  
            function_name = caller_frame.f_code.co_name  
  
            # Try to identify the class  
            class_name = 'Unknown'  
            if 'self' in caller_frame.f_locals:  
                class_name = caller_frame.f_locals['self'].__class__.__name__  
            elif 'cls' in caller_frame.f_locals:  
                class_name = caller_frame.f_locals['cls'].__name__  
  
            return {  
                'filename': filename,  
                'lineno': lineno,  
                'class_name': class_name,  
                'function_name': function_name  
            }        finally:  
            del frame  
  
    def _log(self, level: int, message: str, **kwargs):  
        """  
        Internal method to perform logging with automatic context capture.  
        Args:            level: Log level (logging.DEBUG, logging.INFO, etc.)            message: Message to be logged            **kwargs: Additional context specific to this log        """        caller_info = self._get_caller_info()  
  
        # Format specific context for this log if provided  
        context_str = ""  
        if kwargs:  
            context_parts = []            for key, value in kwargs.items():  
                context_parts.append(f"{key}:{value}")  
            context_str = f"[{']['.join(context_parts)}]"  
  
        # Create custom record  
        # If no class, show only function name        if caller_info['class_name'] == 'Unknown':  
            func_name = caller_info['function_name']  
        else:  
            func_name = f"{caller_info['class_name']}.{caller_info['function_name']}"  
  
        record = self._logger.makeRecord(  
            name=self._logger.name,  
            level=level,  
            fn=caller_info['filename'],  
            lno=int(caller_info['lineno']),  
            msg=message,  
            args=(),  
            exc_info=None,  
            func=func_name,  
            extra={'context': context_str}  
        )  
        self._logger.handle(record)  
  
    def debug(self, message: str, **kwargs):  
        """DEBUG level log."""  
        self._log(logging.DEBUG, message, **kwargs)  
  
    def info(self, message: str, **kwargs):  
        """INFO level log."""  
        self._log(logging.INFO, message, **kwargs)  
  
    def warning(self, message: str, **kwargs):  
        """WARNING level log."""  
        self._log(logging.WARNING, message, **kwargs)  
  
    def error(self, message: str, **kwargs):  
        """ERROR level log."""  
        self._log(logging.ERROR, message, **kwargs)  
  
    def critical(self, message: str, **kwargs):  
        """CRITICAL level log."""  
        self._log(logging.CRITICAL, message, **kwargs)  
  
    def set_level(self, level: str):  
        """  
        Sets the minimum log level.  
        Args:            level: Log level ('DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL')        """        level_map = {  
            'DEBUG': logging.DEBUG,  
            'INFO': logging.INFO,  
            'WARNING': logging.WARNING,  
            'ERROR': logging.ERROR,  
            'CRITICAL': logging.CRITICAL  
        }  
  
        if level.upper() in level_map:  
            self._logger.setLevel(level_map[level.upper()])  
            for handler in self._logger.handlers:  
                handler.setLevel(level_map[level.upper()])  
    def reconfigure_from_settings(self):  
        """  
        Reconfigures the logger based on current Settings configuration.        Useful to ensure configurations are applied after initialization.        """        try:  
            from src.config.settings import settings  
            env = settings.ENV.lower()  
            debug_enabled = settings.DEBUG  
  
            # Priority: DEBUG setting > ENV setting  
            if debug_enabled:  
                new_level = logging.DEBUG  
            else:  
                # If DEBUG=False, use level based on ENV  
                if env == 'development':  
                    new_level = logging.INFO  
                elif env == 'testing':  
                    new_level = logging.WARNING  
                else:  # production or any other  
                    new_level = logging.INFO  
  
            # Apply new level  
            self._logger.setLevel(new_level)  
            for handler in self._logger.handlers:  
                handler.setLevel(new_level)  
        except Exception:  
            # If can't access settings, keep current configuration  
            pass  
  
    def enable_debug(self):  
        """Enables DEBUG logs - useful for development."""  
        self.set_level('DEBUG')  
  
    def disable_debug(self):  
        """Disables DEBUG logs - useful for production."""  
        self.set_level('INFO')  
  
  
# Global instance for easy usage  
Logger = StdLogger.get_instance

```