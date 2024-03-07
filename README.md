git add -A; git commit -m "test"; git push

using System;
using System.Collections;
using System.Collections.Generic;
using System.Linq;
using System.Runtime.Remoting.Messaging;
using System.Threading.Tasks;
using System.Windows.Forms;
using static System.Windows.Forms.VisualStyles.VisualStyleElement;

namespace simi {
    //from last time but with some modifications :) 
    internal class PersonClass : IComparable<PersonClass>, ICloneable{
        
        #region Properties

        #region Age - Without using Properties
        private int _age;
        public int GetAge() {
            return _age; // "this._age" is implicit
        }
        public void SetAge(int value) {
            _age = value; // "this._age" is implicit
        }
        #endregion

        #region Name - Using properties
        private string _name;
        //Read/Write property
        public string Name {
            get { return _name; }
            set { _name = value; }
        }

        //Readonly property
        public string Name2 {
            get { return _name; }
        }
        #endregion

        #region Occupation - Using auto-property
        public OccupationEnum Occupation { get; set; }
        #endregion
        #endregion

        public PersonClass(int age) {
            Console.WriteLine("Constructor(default)");
            _age = age; //equivalent with this._age = age;
        }

        public PersonClass(int age, string name, OccupationEnum occupation) : this(age) {
            Console.WriteLine("Constructor(parameters)");
            Name = name;
            Occupation = occupation;
        }

        //Copy constructor - https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/how-to-write-a-copy-constructor
        public PersonClass(PersonClass previousPerson) : this(previousPerson.GetAge(), previousPerson.Name, previousPerson.Occupation) {
            Console.WriteLine("Copy Constructor");
        }

        //Destructor
        ~PersonClass() {
            //Console.WriteLine("Destructor");
        }

        public override string ToString() {
            return string.Format("Name: {0}, Age: {1},  Occupation: {2}", Name, _age, Occupation);
        }
        

        //ICompareble - must be this way :) 
        public int CompareTo(PersonClass other) {
            //return _age - other.GetAge();          // if our age is less tham the other persons them will be negative/positive 
            return _age.CompareTo(other._age);
        }


        public object Clone() {    //are nev de cast, nu e obj generic
            return new PersonClass(this); //with the use of the copy costructor 

        }
    }
    internal enum OccupationEnum {
        Child = 0,
        Student,
        Employee
    }

    internal class Student : PersonClass, ICloneable {

        public List<float> Grades { get; set; }

        public Student() : base(0) { }

        public Student(int age) : base(age) { }

        public Student(int age, List<float> grades) : base(age) {
            Grades = grades;
        }

        public object Clone() { 
            var other = new Student();
            other.SetAge(GetAge());
            other.Name = Name;
            other.Occupation = Occupation;
            // other.Grades = Grades; shallow copy
            other .Grades = new List<float>();
            foreach (var grade in Grades) {
                other .Grades.Add(grade);   
            }
            return other;
        }


    }

    //sem3
    // interfaces are abstract and virtual- in c# they start with I - and they do only one thing/ have only one task
    internal static class Program {
        [STAThread]
        static void Main() {
            var persons = new List<PersonClass>();     // why it dosen't work with ArrayList ?
            persons.Add(new PersonClass(45, "Erik", OccupationEnum.Employee));
            persons.Add(new PersonClass(25, "Bogdan", OccupationEnum.Student));
            persons.Add(new PersonClass(10, "Alex", OccupationEnum.Child));
            //persons.Sort();                         
            //with a lambda exception - sortati in functie de nume
            persons.Sort((PersonClass o1, PersonClass o2) => { return o1.Name.Com            pareTo(o2.Name); });

            foreach (PersonClass person in persons) {   // we used var initially because there is the posiblillity to recieve onoter thing like now, we expected a var but recieved an objest
                Console.WriteLine(person.ToString());
            }

            var pers1 = new PersonClass(21, "Mihai", OccupationEnum.Student);
            var pers2 = pers1;
            Console.WriteLine(pers1);
            Console.WriteLine(pers2);

            pers2.Name = "Vasile";
            Console.WriteLine(pers1);
            Console.WriteLine(pers2);

            pers2 = pers1.Clone() as PersonClass;
            //pers2 = (PersonClass)pers1.Clone();   //cast explicit

            pers2.Name = "Bogdan";
            Console.WriteLine(pers1);
            Console.WriteLine(pers2);

        }
    }
}
//at home - to look at them :) 
//3.collections -> 5. custom collections -> operator de indexare (Indexer) 
//4.delegates and events 
