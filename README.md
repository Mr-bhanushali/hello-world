import { Component, OnInit, ViewChild } from '@angular/core';
import { Subject } from 'rxjs';
import { DataTableDirective } from 'angular-datatables';


export class Test {
  constructor(
    public code: string,
    public name: string
  ) { }
}

@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  @ViewChild(DataTableDirective) datatableElement: DataTableDirective;

  clients = Array<Test>();
  counter :number =1;
  dtTrigger: Subject<any> = new Subject();
  dtOptions: DataTables.Settings = {};

  ngOnInit(): void {
    this.dtOptions = {
      order: [[0, "asc"]],
      autoWidth: true,
      columns: [
        { title: 'Code', data: 'name' },
        { title: 'Name', data: 'code' },
      ]
    };

    this.clients.push(new Test("a", "a"));
    this.rerender();
  }

  ngOnDestroy(): void {
    this.dtTrigger.unsubscribe();
  }

  rerender(): void {
      this.datatableElement.dtInstance.then((dtInstance: DataTables.Api) => {
        dtInstance.destroy();
        this.dtTrigger.next();
      });
  }

  ngAfterViewInit(): void {
    this.dtTrigger.next();
    this.datatableElement.dtInstance.then((dtInstance: DataTables.Api) => {
      dtInstance.columns().every(function () {
        const that = this;

        // For checked fields
        $('input[type=checkbox]', this.footer()).on('checked change', function () {
          if (this['value'] === 'true') {
            that.search(this['value']).draw();
          } else {
            that.search('').draw();
          }
        });

        $('input[type=text]', this.footer()).on('keyup change', function () {
          console.log("text : " + this['value']);
          if (that.search() !== this['value']) {
            that.search(this['value']).draw();
          }
        });
      });
    });
  }

  addRow() {
    this.counter = this.counter +1;
    this.clients.push(new Test("ac/" + this.counter, "ac" + this.counter));
    this.rerender();
  }

  removeRow() {
    this.clients = this.clients.slice(1);
    this.rerender();
  }


  filter(){
     this.clients = this.clients.filter(t=> t.name.includes("2"));
         this.rerender();
  }
  clear(){
    this.clients = [];
     this.rerender();
  }


  DownloadJSON2CSV()
    {
      var objArray = this.clients;
        var array = typeof objArray != 'object' ? JSON.parse(objArray) : objArray;

        var str = '';
var line = ''
        for (var index in array[0]) {
                line += index + ',';
            }
        

        line = line.slice(0, -1);
        str += line + '\r\n';

        for (var i = 0; i < array.length; i++) {
            var line = '';

            for (var index in array[i]) {
                line += array[i][index] + ',';
            }

            // Here is an example where you would wrap the values in double quotes
            // for (var index in array[i]) {
            //    line += '"' + array[i][index] + '",';
            // }

            line.slice(0,line.length - 1); 

            str += line + '\r\n';
           
        }
         console.log(str);
       // window.open( "data:text/csv;charset=utf-8," + escape(str))



  
    var downloadLink = document.createElement("a");
    var blob = new Blob(["\ufeff", str]);
    var url = URL.createObjectURL(blob);
    downloadLink.href = url;
    downloadLink.download = "data.csv";

    document.body.appendChild(downloadLink);
    downloadLink.click();
    document.body.removeChild(downloadLink);



    }
}
\\\\\\\\\\\\\\\\\\\\\\\\\

<table datatable [dtOptions]="dtOptions" [dtTrigger]="dtTrigger" class="table table-striped table-bordered">
	<thead>
		<tr>
			<th>code</th>
			<th>name</th>
		</tr>
	</thead>
	<tbody>
		<tr *ngFor="let client of clients">
			<td> {{client.code}} </td>
			<td> {{client.name}} </td>
		</tr>
	</tbody>
	<tfoot>
		<tr>
			<th>
				<input type="text" placeholder="code" name="search-code" />
			</th>
			<th>
				<input type="text" placeholder="name" name="search-name" />
			</th>
		</tr>
	</tfoot>
</table>
<br/>
<br/>
<div class="btn-group btn-group-justified">
	<a class="btn btn-default">
		<button type="submit" class="btn btn-default" (click)="addRow()">Creation</button>
	</a>
</div>
<div class="btn-group btn-group-justified">
	<a class="btn btn-default">
		<button type="submit" class="btn btn-default" (click)="removeRow()">Remove</button>
	</a>
</div>
<div class="btn-group btn-group-justified">
	<a class="btn btn-default">
		<button type="submit" class="btn btn-default" (click)="filter()">filter</button>
	</a>
</div>
<div class="btn-group btn-group-justified">
	<a class="btn btn-default">
		<button type="submit" class="btn btn-default" (click)="clear()">clear</button>
	</a>
</div>

<div class="btn-group btn-group-justified">
	<a class="btn btn-default">
		<button type="submit" class="btn btn-default" (click)="DownloadJSON2CSV()">DownloadJSON2CSV</button>
	</a>
</div>
