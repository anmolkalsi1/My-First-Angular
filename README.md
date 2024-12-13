# My-First-Angular      https://github.com/mathewhaug/MAD307F24FinalReview
ng generate pipe AgePipe
ng generate pipe ExperiencePipe
ng generate pipe RatingPipe

import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'age' })
export class AgePipe implements PipeTransform {
  transform(age: number): string {
    return age > 1 ? `${age} years` : `${age} year old`;
  }
}
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'experience' })
export class ExperiencePipe implements PipeTransform {
  transform(experience: number): string {
    return experience > 0
      ? `${experience} years of experience`
      : 'New sitter';
  }
}
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'rating' })
export class RatingPipe implements PipeTransform {
  transform(rating: number): string {
    const stars = '★'.repeat(rating) + '☆'.repeat(5 - rating);
    return stars;
  }
}
ng generate directive HighlightDirective
ng generate directive SitterStatusDirective
import { Directive, ElementRef, Renderer2, HostListener } from '@angular/core';

@Directive({ selector: '[appHighlight]' })
export class HighlightDirective {
  constructor(private el: ElementRef, private renderer: Renderer2) {}

  @HostListener('mouseenter') onMouseEnter() {
    this.renderer.addClass(this.el.nativeElement, 'highlight');
  }

  @HostListener('mouseleave') onMouseLeave() {
    this.renderer.removeClass(this.el.nativeElement, 'highlight');
  }
}
.highlight {
  background-color: yellow;
}


import { Directive, Input, ElementRef, Renderer2, OnChanges } from '@angular/core';

@Directive({ selector: '[appSitterStatus]' })
export class SitterStatusDirective implements OnChanges {
  @Input() appSitterStatus: string;

  constructor(private el: ElementRef, private renderer: Renderer2) {}

  ngOnChanges(): void {
    const color = this.appSitterStatus === 'Available' ? 'green' : 'red';
    this.renderer.setStyle(this.el.nativeElement, 'color', color);
    this.renderer.setProperty(this.el.nativeElement, 'textContent', this.appSitterStatus);
  }
}
ng generate component ModifySitter
import { Component, OnInit } from '@angular/core';
import { FormBuilder, FormGroup, Validators } from '@angular/forms';

@Component({
  selector: 'app-modify-sitter',
  templateUrl: './modify-sitter.component.html',
  styleUrls: ['./modify-sitter.component.scss']
})
export class ModifySitterComponent implements OnInit {
  sitterForm: FormGroup;

  constructor(private fb: FormBuilder) {
    this.sitterForm = this.fb.group({
      name: ['', Validators.required],
      age: [null, [Validators.required, Validators.min(1), Validators.max(100)]],
      experience: [null, Validators.required],
      specialty: ['', Validators.required],
      availability: [true, Validators.required]
    });
  }

  ngOnInit(): void {}

  onSubmit(): void {
    if (this.sitterForm.valid) {
      console.log('Form Data:', this.sitterForm.value);
    }
  }
}


<form [formGroup]="sitterForm" (ngSubmit)="onSubmit()">
  <mat-form-field appearance="outline">
    <mat-label>Name</mat-label>
    <input matInput formControlName="name" />
  </mat-form-field>

  <mat-form-field appearance="outline">
    <mat-label>Age</mat-label>
    <input matInput type="number" formControlName="age" />
  </mat-form-field>

  <mat-form-field appearance="outline">
    <mat-label>Experience</mat-label>
    <input matInput type="number" formControlName="experience" />
  </mat-form-field>

  <mat-form-field appearance="outline">
    <mat-label>Specialty</mat-label>
    <input matInput formControlName="specialty" />
  </mat-form-field>

  <mat-checkbox formControlName="availability">Available</mat-checkbox>

  <button mat-raised-button color="primary" type="submit" [disabled]="!sitterForm.valid">Save</button>
</form>

add this on main.ts 
import { provideRouter } from '@angular/router';
import { ModifySitterComponent } from './modify-sitter/modify-sitter.component';

bootstrapApplication(AppComponent, {
  providers: [
    provideRouter([{ path: 'modify-sitter', component: ModifySitterComponent }])
  ]
});

<table mat-table [dataSource]="sitters" class="mat-elevation-z8">
  <ng-container matColumnDef="name">
    <th mat-header-cell *matHeaderCellDef>Name</th>
    <td mat-cell *matCellDef="let sitter">{{ sitter.name }}</td>
  </ng-container>
  <ng-container matColumnDef="specialty">
    <th mat-header-cell *matHeaderCellDef>Specialty</th>
    <td mat-cell *matCellDef="let sitter">{{ sitter.specialty }}</td>
  </ng-container>
  <ng-container matColumnDef="availability">
    <th mat-header-cell *matHeaderCellDef>Availability</th>
    <td mat-cell *matCellDef="let sitter">{{ sitter.available ? 'Available' : 'Unavailable' }}</td>
  </ng-container>
  <tr mat-header-row *matHeaderRowDef="['name', 'specialty', 'availability']"></tr>
  <tr mat-row *matRowDef="let row; columns: ['name', 'specialty', 'availability']"></tr>
</table>

<mat-card>
  <mat-card-title>{{ sitter.name }}</mat-card-title>
  <mat-card-content>
    <p>Age: {{ sitter.age }}</p>
    <p>Experience: {{ sitter.experience }}</p>
    <p>Specialty: {{ sitter.specialty }}</p>
    <p>Rating: {{ sitter.rating }}</p>
    <p>Status: <span appSitterStatus [appSitterStatus]="sitter.available ? 'Available' : 'Unavailable'"></span></p>
  </mat-card-content>
</mat-card>
