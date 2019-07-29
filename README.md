# summaries
A set of personal summaries. 

## Angular

### [Reusable forms article](https://blog.angularindepth.com/reducing-the-forms-boilerplate-make-your-angular-forms-reusable-ee06d7c07f47)

Main points:

 - **You should design the form fields in the reusable way if this form will be used more than one time in your application**.
 - **If your form represents the business entity you should make it reusable.**
 - **Try to make self-validated forms.**

The example of the reusable form field with self-contained validation:

```typescript
import { Component, OnInit, Input, ViewChild, ElementRef, Self, Optional, forwardRef } from '@angular/core';
import {
  ControlValueAccessor, NG_VALUE_ACCESSOR, Validator, AbstractControl,
  ValidatorFn, Validators, NG_VALIDATORS, NgControl, NgForm
} from '@angular/forms';

@Component({
  selector: 'app-generic-input',
  templateUrl: './generic-input.component.html',
  styleUrls: ['./generic-input.component.scss'],
  // providers: [{
  //   provide: NG_VALUE_ACCESSOR,
  //   multi: true,
  //   useExisting: GenericInputComponent
  // },
  // {
  //   provide: NG_VALIDATORS,
  //   multi: true,
  //   useExisting: GenericInputComponent
  // }]
})
export class GenericInputComponent implements ControlValueAccessor, Validator, OnInit {


  @ViewChild('input') input: ElementRef;
  disabled;

  @Input() type = 'text';
  @Input() isRequired: boolean = false;
  @Input() pattern: string = null;
  @Input() label: string = null;
  @Input() placeholder: string;
  @Input() errorMsg: string;

  constructor(@Self() public controlDir: NgControl) {
    this.controlDir.valueAccessor = this;
  }

  ngOnInit() {
    const control = this.controlDir.control;
    const validators: ValidatorFn[] = control.validator ? [control.validator] : [];
    if (this.isRequired) {
      validators.push(Validators.required);
    }
    if (this.pattern) {
      validators.push(Validators.pattern(this.pattern));
    }

    control.setValidators(validators);
    control.updateValueAndValidity();
  }

  onChange(event) { }

  onTouched() { }

  writeValue(obj: any): void {
    this.input.nativeElement.value = obj;
  }
  registerOnChange(fn: any): void {
    this.onChange = fn;
  }
  registerOnTouched(fn: any): void {
    this.onTouched = fn;
  }
  setDisabledState?(isDisabled: boolean): void {
    this.disabled = isDisabled;
  }

  validate(c: AbstractControl): { [key: string]: any; } {
    const validators: ValidatorFn[] = [];
    if (this.isRequired) {
      validators.push(Validators.required);
    }
    if (this.pattern) {
      validators.push(Validators.pattern(this.pattern));
    }

    return validators;
  }


}
```

[Working demos](https://stackblitz.com/edit/angular-generic-forms-examples)


