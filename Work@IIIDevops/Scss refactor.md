.
├── default -> theme 1
├── sea -> theme 2
├── green -> theme 3
├── index.scss -> entry point
├── element-ui.scss -> .el-xxx css
├── custom.scss -> anything without .el-xxx css
├── mixin.scss -> scss mixin
├── sidebar.scss -> sidebar
├── table.scss -> pure table for print
├── ~~theme.scss~~
├── transition.scss -> transition
└── variables.scss -> variables

I am modifying the files under `styles` folder, and here are some rules:
- all css are `sorted alphabetically`
- please make good use of `mixin` to encapsulate repeated and heavily used css
- element ui css in `element-ui.scss`, while others in `custom.scss`

Here are some examples below

#### Increase part in mixin.scss

``` css
@mixin background-border-color($backgroundColor, $borderColor) {  
  background-color: $backgroundColor !important;  
  border-color: $borderColor !important;
}  
  
@mixin button($mainColor) {  
  background-color: $mainColor !important;  
  color: white !important;  
  &:hover, &:active, &:focus {    
	  @include background-border-color($mainColor, $mainColor);    
	  color:white !important;  
  }
}  
  
@mixin button-reverse($mainColor) {  
  border-color: $buttonBorderColor;  
  color: $mainColor !important;  
  background-color: white !important;  
  &:focus {    
	  border-color: $buttonFocusBorderColor;    
	  color: $mainColor !important;    
	  background-color: white !important;  
  }  
  &:hover, &:active {    
	  background-color: $mainColor !important;    
	  border-color: $mainColor !important;    
	  color: white !important;  
  }
}  
  
@mixin flexbox-center {  
  display: flex;  
  align-items: center;  
  justify-content: center;
}
```

#### mixin in custom.scss

``` css
@import './mixin.scss';

.buttonPrimary {  
	@include button($buttonPrimary);
}

.buttonPrimaryReverse {  
	@include button-reverse($buttonPrimary);
}
```

#### Check list
- [ ] tabs layout
  - community
	  - ProjectSettings
  - shared
	  - Milestone
	  - ProjectOverview
	  - IssueDetail / Desktop
	  - AddSubIssue
  - 2.0
	  - ProjectSettings
	  - ProjectSettings (QA)
	  - TraceabilityMatrix
	  - ApplicationSetting
	  - TestCasePostman
	  - SystemDeploySettings
	  - EditCluster
- [ ] color or heavily used css in components move to global css file
- [ ] common css to scss mixin
- [ ] theme feature
- [ ] transition css to global