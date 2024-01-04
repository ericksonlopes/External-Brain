as_teardown

start_band >> band_scraping >> decide_task_band >> [band_analyzer, skip_analyzer_band]  
band_analyzer >> sleep_after_band >> finish_band  
skip_analyzer_band >> finish_band.as_teardown(setups=start_band)