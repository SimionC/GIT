git add -A; git commit -m "test"; git push

steps Paw - 6
Car Dealership
1.  You are asked to develop a Windows Forms application for a multibrand car dealer. The company
sells various car models from several manufacturers (brands). Define the Manufacturer class (Id, Name) and
the Car class (Id, Manufacturing Date, Price, Manufacturerld).(1p+)

	!  U can create the variables with propfull
	1. create the class Manufacturer with Id and Name + toString(for View)
		 public override string ToString() => $"{Id} {Name}";
	2. create the class Car with Id, Manufacturing Date, Price and Manufacturerld

2. The manufacturers will be loaded from the Manufacturer.txt file. The text file should be created using
a text editor at your choice and should contain at least three entries.(0.5p)

	1. create a txt file with the data -> properties ->copy always
	2. in MainForm add: 
		public List<Manufacturer> Manufacturers = new List<Manufacturer>();

		private void LoadFromFile() {

    		string exePath = Assembly.GetExecutingAssembly().Location;
    		string folderPath = new FileInfo(exePath).DirectoryName;
    		string txtFilePath = "Manufacturer.txt";
		
    		string[] lines = File.ReadAllLines(Path.Combine(folderPath, txtFilePath));
    		foreach(string line in lines) {
    		    var items = line.Split(' ');
    		    Manufacturers.Add(new Manufacturer() {
    		        Id = int.Parse(items[0]),
    		        Name = items[1]
    		    });
    		}
		}
	3. don't forget to call it in public MainForm(){LoadFromFile();}


3. The instances of the Car class will be added using a secondary form, in which the user will be able to
choose the manufacturer using a ComboBox control. The price field should allow only digits to be entered.
The ManufacturingDate should be in either the current or the previous year. The validation errors will be
displayed next to the corresponding fields when the user changes the focus to another control, as well as
when the user clicks the "Add" button. The application should not crash if the user inputs invalid information.(2p)

	1. create the AddForm, DON'T FORGET TO ADD BINDINGSOURCE  
	2. Add OnPropetyChanged like: public class Car: INotifyPropertyChanged{...}
	3. implement the interface : public event PropertyChangedEventHandler PropertyChanged;
	4. add the funtion:

		public void OnPropertyChanged(string propertyName){
   			 if(PropertyChanged != null) 
        		PropertyChanged(this, new PropertyChangedEventArgs(propertyName)); 
		}

	5. call it in the setters of the variables: OnPropertyChanged(nameof(Id));
	6. BUILD IT- and put the settings for each attribute in the form

			public Car SelectedItem { get; internal set; }

			public AddForm(){
    			InitializeComponent();
    			SelectedItem = new Car();
    			this.bindingSource1.DataSource = SelectedItem;
			}


			BUT combobox(cbManufacturer) - DropDownList + it needs the connection with the bindingSource:

			private List<Manufacturer> Manufacturers { get; set; }
			public void BindManufacturers(List<Manufacturer> list){
     			Manufacturers = list;
     			this.cbManufacturer.DataSource = Manufacturers;
 			}

 			private void cbManufacturer_SelectedValueChanged(object sender, EventArgs e){
    			this.SelectedItem.ManufacturerId = (cbManufacturer.SelectedItem as Manufacturer).Id;
 			}

    6.put the Error provider validations in the Savebtn:
    	bool ok = true;
		if (numericUpDown1.Value < 0){
		    errorProvider1.SetError(numericUpDown1, "value must be positive");
		    ok = false;
		} 
		else errorProvider1.SetError(numericUpDown1, "");
		
		if(dateTimePicker1.Value.Year < 2023 || dateTimePicker1.Value.Year > 2024){
		    errorProvider1.SetError(dateTimePicker1, "year must be 2023 or 2024");
		    ok = false;
		}
		else errorProvider1.SetError(dateTimePicker1, "");
		
		if (ok == true){ 
		    this.DialogResult = DialogResult.OK;
		    return;
		}



4. The instances of the class will be stored in a List<T> collection (or any other collection type) and will be displayed in the main form using a DataGridView control.(0.5p)
	1. add GridView and Add btn 
	2. configure the btn:
		public BindingList<Car> Cars = new BindingList<Car>();
		private void btnAdd_Click(object sender, EventArgs e){
   			var addform = new AddForm();
   			addform.BindManufacturers(Manufacturers);
   			addform.ShowDialog();
    		Cars.Add(addform.SelectedItem);
		}
		//SelectedItem in from AddForm: public Car SelectedItem { get; internal set; }
	3. put the data in the gridView

		public MainForm(){
    		InitializeComponent();
    		LoadFromFile();

    		Cars.ListChanged += Cars_ListChanged;
		}

		private void Cars_ListChanged(object sender, ListChangedEventArgs e){
    		dataGridView1.DataSource = Cars;
    		dataGridView1.Refresh();
		}


5. Add a contextual menu to the DataGridView, allowing the user to edit and delete the cars. Editing the
information for a car should be performed in a secondary form. (1p)

	1. add the context menu strip + set edit/delete + rowselect in grid 
	2. set the action for context(MouseClick)

		 private void dataGridView1_MouseClick(object sender, MouseEventArgs e){
     		if (e.Button != MouseButtons.Right)
     		    return;
     		this.contextMenuStrip.Show(this.dataGridView1, e.X, e.Y);
 		}

 	3. Set edit and delete

 		public AddForm(Car car) {
    		InitializeComponent();
    		SelectedItem = car;
    		this.bindingSource1.DataSource = SelectedItem;
		}

	    private void editToolStripMenuItem_Click(object sender, EventArgs e){
    		if (dataGridView1.SelectedRows.Count == 0)
    		    return;
    		var carToEdit = dataGridView1.SelectedRows[0].DataBoundItem as Car;          
    		var addform = new AddForm(carToEdit);
    		addform.BindManufacturers(Manufacturers);
    		addform.ShowDialog();
		}		

        private void deleteToolStripMenuItem_Click(object sender, EventArgs e)		{
    		if (dataGridView1.SelectedRows.Count == 0)
    		    return;
    		var carToEdit = dataGridView1.SelectedRows[0].DataBoundItem as Car;
    		Cars.Remove(carToEdit);
		}


6. Implement the IComparable<T> / IComparable interface in order to allow the user to sort the cars in
ascending order based on the Price. The list of cars should be kept sorted all the time.(1p)
	1. implement IComparable<Car>
	2. override the function: public int CompareTo(Car other) => Price.CompareTo(other.Price);
	3. sort it in Car_ListChanged
		var cars = Cars.ToList();
		cars.Sort();

