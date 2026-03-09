class PdfEntity(BaseModel):  
    model_config = ConfigDict(populate_by_name=True)  
  
    id: str  
    grade: Optional[str] = Field(default=None, alias="grade")  
  
    @model_validator(mode="before")  
    def handle_grade_aliases(self, data):  
        if 'grade' not in data and 'bookGrade' in data:  
            data['grade'] = data['bookGrade']  
        return data