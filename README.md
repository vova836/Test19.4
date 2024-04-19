//Задача1
using System;
using System.Collections.Generic;
using System.Linq;

class TourAgency
{
    public string Name { get; set; }
    public int YearEstablished { get; set; }
    public string INN { get; set; }
    public decimal Profit { get; set; }

    public TourAgency(string name, int yearEstablished, string inn, decimal profit)
    {
        Name = name;
        YearEstablished = yearEstablished;
        INN = inn;
        Profit = profit;
    }

    public void GetInfo()
    {
        Console.WriteLine($"Tour Agency: {Name}\nYear Established: {YearEstablished}\nINN: {INN}\nProfit: {Profit}\n");
    }
}

class Program
{
    static void Main()
    {
        List<TourAgency> agencies = new List<TourAgency>
        {
            new TourAgency("Agency1", 2000, "123456789", 100000),
            new TourAgency("Agency2", 2010, "987654321", 150000),
            new TourAgency("Agency3", 1995, "456789123", 120000)
        };

        decimal totalProfit = agencies.Sum(a => a.Profit);
        Console.WriteLine($"Total profit of all agencies: {totalProfit}");

        TourAgency maxProfitAgency = agencies.OrderByDescending(a => a.Profit).First();
        Console.WriteLine($"Agency with the highest profit: {maxProfitAgency.Name} with profit of {maxProfitAgency.Profit}");

        string bankruptAgencyINN = "987654321"; // INN of bankrupt agency
        var bankruptAgency = agencies.FirstOrDefault(a => a.INN == bankruptAgencyINN);
        if (bankruptAgency != null)
        {
//Задача2
```csharp
using System;
using System.Collections.Generic;
using System.IO;

// Интерфейс для обновления или аннулирования пропуска
interface IPassUpdate
{
    void UpdatePass();
    void CancelPass();
}

// Базовый класс для пропусков
class Pass
{
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public string MiddleName { get; set; }
    public string Faculty { get; set; }

    public Pass(string lastName, string firstName, string middleName, string faculty)
    {
        LastName = lastName;
        FirstName = firstName;
        MiddleName = middleName;
        Faculty = faculty;
    }
}

// Класс для пропусков преподавателей
class TeacherPass : Pass, IPassUpdate
{
    public string Department { get; set; }

    public TeacherPass(string lastName, string firstName, string middleName, string faculty, string department) : base(lastName, firstName, middleName, faculty)
    {
        Department = department;
    }

    public void UpdatePass()
    {
        Console.WriteLine($"Teacher pass for {FirstName} {LastName} updated.");
    }

    public void CancelPass()
    {
        Console.WriteLine($"Teacher pass for {FirstName} {LastName} cancelled.");
    }
}

// Класс для пропусков студентов
class StudentPass : Pass, IPassUpdate
{
    public int YearOfAdmission { get; set; }

    public StudentPass(string lastName, string firstName, string middleName, string faculty, int yearOfAdmission) : base(lastName, firstName, middleName, faculty)
    {
        YearOfAdmission = yearOfAdmission;
    }

    public void UpdatePass()
    {
        Console.WriteLine($"Student pass for {FirstName} {LastName} updated.");
    }

    public void CancelPass()
    {
        Console.WriteLine($"Student pass for {FirstName} {LastName} cancelled.");
    }
}

// Класс для пропусков строителей
class BuilderPass : Pass, IPassUpdate
{
    public DateTime StartDate { get; set; }
    public DateTime EndDate { get; set; }

    public BuilderPass(string lastName, string firstName, string middleName, string faculty, DateTime startDate, DateTime endDate) : base(lastName, firstName, middleName, faculty)
    {
        StartDate = startDate;
        EndDate = endDate;
    }

    public void UpdatePass()
    {
        Console.WriteLine($"Builder pass for {FirstName} {LastName} updated.");
    }

    public void CancelPass()
    {
        Console.WriteLine($"Builder pass for {FirstName} {LastName} cancelled.");
    }
}

class Program
{
    static void Main()
    {
        List<object> register = new List<object>();

        // Считываем информацию о пропусках из файла и создаем объекты
        try
        {
            using (StreamReader sr = new StreamReader("passes.txt"))
            {
                string line;
                while ((line = sr.ReadLine()) != null)
                {
                    string[] data = line.Split(',');
                    if (data.Length > 0)
                    {
                        if (data[0] == "Teacher")
                        {
                            register.Add(new TeacherPass(data[1], data[2], data[3], data[4], data[5]));
                        }
                        else if (data[0] == "Student")
                        {
                            register.Add(new StudentPass(data[1], data[2], data[3], data[4], int.Parse(data[5])));
                        }
                        else if (data[0] == "Builder")
                        {
                            register.Add(new BuilderPass(data[1], data[2], data[3], data[4], DateTime.Parse(data[5]), DateTime.Parse(data[6])));
                        }
                    }
                }
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("The file could not be read:");
            Console.WriteLine(e.Message);
        }

        // Вывод информации о пропусках
        foreach (object pass in register)
        {
            if (pass is TeacherPass)
            {
                TeacherPass teacherPass = (TeacherPass)pass;
                Console.WriteLine($"Teacher:
{teacherPass.LastName} {teacherPass.FirstName}, Faculty: {teacherPass.Faculty}, Department: {teacherPass.Department}");
            }
            else if (pass is StudentPass)
            {
                StudentPass studentPass = (StudentPass)pass;
                Console.WriteLine($"Student: {studentPass.LastName} {studentPass.FirstName}, Faculty: {studentPass.Faculty}, Year of Admission: {studentPass.YearOfAdmission}");
            }
            else if (pass is BuilderPass)
            {
                BuilderPass builderPass = (BuilderPass)pass;
                Console.WriteLine($"Builder: {builderPass.LastName} {builderPass.FirstName}, Faculty: {builderPass.Faculty}, Start Date: {builderPass.StartDate}, End Date: {builderPass.EndDate}");
            }
        }

        // Аннулирование пропусков по правилам
        foreach (object pass in register)
        {
            if (pass is StudentPass || (pass is BuilderPass && ((BuilderPass)pass).EndDate < DateTime.Now))
            {
                ((IPassUpdate)pass).CancelPass();
            }
            else if (pass is TeacherPass)
            {
                ((IPassUpdate)pass).UpdatePass();
            }
        }
    }
}
```

Это пример кода на C#, который реализует иерархию классов для пропусков студентов, преподавателей и строителей, а также интерфейс для обновления и аннулирования пропуска. Код загружает информацию о пропусках из файла, выводит информацию о пропусках, аннулирует пропуски согласно правилам и 
