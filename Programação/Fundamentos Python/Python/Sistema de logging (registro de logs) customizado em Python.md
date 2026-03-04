```python
import inspect  
import logging  
import os  
import sys  
from abc import abstractmethod, ABC  
from datetime import datetime  
from typing import Optional, Dict, Set  
  
LOG_FORMAT = "{asctime} | {levelname} | {filepath}:{lineno} | {message}"  
  
  
class ILogger(ABC):  
  
    @abstractmethod  
    def _is_allowed(self, level_name: str) -> bool:  
        raise NotImplementedError  
  
    @abstractmethod  
    def info(self, message: str, context: Optional[Dict] = None) -> None:  
        raise NotImplementedError  
  
    @abstractmethod  
    def debug(self, message: str, context: Optional[Dict] = None) -> None:  
        raise NotImplementedError  
  
    @abstractmethod  
    def warning(self, message: str, context: Optional[Dict] = None) -> None:  
        raise NotImplementedError  
  
    @abstractmethod  
    def error(self, error: Exception, context: Optional[Dict] = None) -> None:  
        raise NotImplementedError  
  
    @abstractmethod  
    def critical(self, message: str, context: Optional[Dict] = None) -> None:  
        raise NotImplementedError  
  
  
def get_logger_module_files(base_dir=None):  
    """  
    Returns a set with normalized (absolute) paths of ALL .py files    inside src/infrastructure/logger, including subdirectories.    This list adapts dynamically to all files present in the logger infra.    """    # Automatically determines the absolute logger directory, even if the project is moved  
    if base_dir is None:  
        base_dir = os.path.join(  
            os.path.dirname(os.path.abspath(__file__))  
        )    logger_files = set()  
    for root, _, files in os.walk(base_dir):  
        for file in files:  
            if file.endswith('.py'):  
                logger_files.add(os.path.abspath(os.path.join(root, file)))  
    return logger_files  
  
  
def get_log_record(level: str, message: str):  
    """  
    Extracts detailed context from the frame where the log was originally called.    Returns a dict with all fields for the standard log template.    This context excludes any frame from infrastructure python files found in the logger directory.    """    logger_files = get_logger_module_files()  
    stack = inspect.stack()  
    cls_name = ""  
    frame_best = stack[1]  
    for frame_info in stack:  
        filename_abs = os.path.abspath(frame_info.filename)  
        # Ignore all frames in infra/logger (dynamic detection)  
        if filename_abs not in logger_files:  
            self_obj = frame_info.frame.f_locals.get('self', None)  
            if self_obj:  
                cls_name = type(self_obj).__name__  
            frame_best = frame_info  
            break  
  
    asctime = datetime.now().strftime('%Y-%m-%d %H:%M:%S,%f')[:-3]  
    filename = os.path.basename(frame_best.filename)  
    filepath = os.path.abspath(frame_best.filename)  
    lineno = frame_best.lineno  
    func_name = frame_best.function  
  
    return {  
        'asctime': asctime,  
        'levelname': level.upper(),  
        'filename': filename,  
        'filepath': filepath,  
        'lineno': lineno,  
        'class': cls_name,  
        'funcName': func_name,  
        'message': message  
    }  
  
def get_allowed_levels() -> Set[int]:  
    """Return allowed logging levels based on settings.LOG_LEVELS (comma-separated names).  
  
    Example: settings.LIST_LOG_LEVELS = "INFO,ERROR" -> {logging.INFO, logging.ERROR}    """    raw = ["INFO", "DEBUG", "WARNING", "ERROR", "CRITICAL"]  
  
    return _parse_allowed_levels(raw)  
  
  
def _parse_allowed_levels(raw: str) -> Set[int]:  
    """Parse a comma-separated string like "INFO,ERROR" into a set of logging levels (ints)."""  
    level_map = {  
        "DEBUG": logging.DEBUG,  
        "INFO": logging.INFO,  
        "WARNING": logging.WARNING,  
        "ERROR": logging.ERROR,  
        "CRITICAL": logging.CRITICAL,  
    }  
    allowed: Set[int] = set()  
    if not raw:  
        return allowed  
  
    for level in raw:  
        if level in level_map:  
            allowed.add(level_map[level])    return allowed  
  
  
class StdLogger(ILogger):  
    """  
    Standard logger that uses Python's built-in logging instead of loguru.    Keeps compatibility with the ILogger interface.    """  
    def __init__(self, log_format: str, name: Optional[str] = None) -> None:  
        self.log_format = log_format  
        self.service_name = name or "std-logger"  
  
        # Create a unique logger instance to avoid conflicts  
        self._logger = logging.getLogger(f'std_logger_{self.service_name}_{id(self)}')  
  
        # Remove any existing handlers  
        for handler in self._logger.handlers[:]:  
            self._logger.removeHandler(handler)  
  
        # Set the logger to accept all levels (NOTSET)  
        # This prevents it from inheriting the root logger level        self._logger.setLevel(logging.NOTSET)  
        self._logger.propagate = False  
  
        # Configure handler to stdout  
        console_handler = logging.StreamHandler(sys.stdout)  
        # Also set the handler to accept all levels  
        console_handler.setLevel(logging.NOTSET)  
  
        # Simple formatter that just prints the formatted message  
        formatter = logging.Formatter('%(message)s')  
        console_handler.setFormatter(formatter)        self._logger.addHandler(console_handler)  
  
        # Force logger to not inherit from parent (bypass root logger's level)  
        self._logger.parent = None  
  
        self.allowed_levels = get_allowed_levels()  
  
    def _is_allowed(self, level_name: str) -> bool:  
        try:  
            if not self.allowed_levels:  
                return False  
            level = getattr(logging, level_name.upper(), None)  
            return level in self.allowed_levels  
        except Exception:  
            return True  
  
    def info(self, message: str, context: Optional[Dict] = None) -> None:  
        """Log at INFO level."""  
        if self._is_allowed('INFO'):  
            msg = message            if context:  
                msg += f" | context={context}"  
            ctx = get_log_record('INFO', msg)  
            formatted_message = self.log_format.format(**ctx)  
            self._logger.info(formatted_message)  
  
    def debug(self, message: str, context: Optional[Dict] = None) -> None:  
        """Log at DEBUG level."""  
        if self._is_allowed('DEBUG'):  
            msg = message            if context:  
                msg += f" | context={context}"  
            ctx = get_log_record('DEBUG', msg)  
            formatted_message = self.log_format.format(**ctx)  
            self._logger.debug(formatted_message)  
  
    def warning(self, message: str, context: Optional[Dict] = None) -> None:  
        """Log at WARNING level."""  
        if self._is_allowed('WARNING'):  
            msg = message            if context:  
                msg += f" | context={context}"  
            ctx = get_log_record('WARNING', msg)  
            formatted_message = self.log_format.format(**ctx)  
            self._logger.warning(formatted_message)  
  
    def error(self, error: Exception, context: Optional[Dict] = None) -> None:  
        """Log at ERROR level with optional exception/context support."""  
        if self._is_allowed('ERROR'):  
            msg = f"{error}"  
            if context:  
                msg += f" | context={context}"  
  
            ctx = get_log_record('ERROR', msg)  
            formatted_message = self.log_format.format(**ctx)  
            self._logger.error(formatted_message)  
  
    # Additional methods for compatibility with other implementations  
    def critical(self, message: str, context: Optional[Dict] = None) -> None:  
        """Log at CRITICAL level."""  
        if self._is_allowed('CRITICAL'):  
            msg = message            if context:  
                msg += f" | context={context}"  
            ctx = get_log_record('CRITICAL', msg)  
            formatted_message = self.log_format.format(**ctx)  
            self._logger.critical(formatted_message)  
  
  
class Logger:  
    def __init__(self) -> None:  
        delegate = StdLogger(LOG_FORMAT)  
  
        self._logger = delegate  
  
    def __getattr__(self, name):  
        return getattr(self._logger, name)  
  
  
logger = Logger()  
  
if __name__ == '__main__':  
    logger.info("This is an info message")  
    logger.debug("This is a debug message")  
    logger.warning("This is a warning message")  
    logger.error(Exception("This is an error message"))  
    logger.critical("This is a critical message")
```