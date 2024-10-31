using System;
using System.Collections.Generic;

namespace CarServiceSystem
{
    // Car class
    public class Car
    {
        public int Id { get; set; }
        public string Make { get; set; }
        public string Model { get; set; }
        public int Year { get; set; }
        public string LicensePlate { get; set; }
        public string OwnerName { get; set; }
        public string OwnerPhone { get; set; }

        public Car(string make, string model, int year, string licensePlate, string ownerName, string ownerPhone)
        {
            Make = make;
            Model = model;
            Year = year;
            LicensePlate = licensePlate;
            OwnerName = ownerName;
            OwnerPhone = ownerPhone;
        }
    }

    // Service class
    public class Service
    {
        public int Id { get; set; }
        public string Description { get; set; }
        public double Price { get; set; }

        public Service(string description, double price)
        {
            Description = description;
            Price = price;
        }
    }

    // ServiceRequest class
    public class ServiceRequest
    {
        public int Id { get; set; }
        public Car Car { get; set; }
        public List<Service> Services { get; set; }
        public DateTime RequestDate { get; set; }
        public bool IsCompleted { get; set; }

        public ServiceRequest(Car car, List<Service> services)
        {
            Car = car;
            Services = services;
            RequestDate = DateTime.Now;
            IsCompleted = false;
        }
    }

    // ServiceManager class
    public class ServiceManager
    {
        private List<Car> _cars = new List<Car>();
        private List<Service> _services = new List<Service>();
        private List<ServiceRequest> _requests = new List<ServiceRequest>();
        private int _nextCarId = 1;
        private int _nextServiceId = 1;
        private int _nextRequestId = 1;

        public void AddCar(string make, string model, int year, string licensePlate, string ownerName, string ownerPhone)
        {
            var car = new Car(make, model, year, licensePlate, ownerName, ownerPhone) { Id = _nextCarId };
            _cars.Add(car);
            _nextCarId++;
        }

        public void AddService(string description, double price)
        {
            var service = new Service(description, price) { Id = _nextServiceId };
            _services.Add(service);
            _nextServiceId++;
        }

        public void CreateServiceRequest(int carId, List<int> serviceIds)
        {
            var car = _cars.FirstOrDefault(c => c.Id == carId);
            if (car == null)
            {
                Console.WriteLine("Car not found.");
                return;
            }

            var services = _services.Where(s => serviceIds.Contains(s.Id)).ToList();
            if (services.Count != serviceIds.Count)
            {
                Console.WriteLine("Invalid service ID(s).");
                return;
            }

            var request = new ServiceRequest(car, services) { Id = _nextRequestId };
            _requests.Add(request);
            _nextRequestId++;
            Console.WriteLine("Service request created successfully.");
        }

        public void CompleteServiceRequest(int requestId)
        {
            var request = _requests.FirstOrDefault(r => r.Id == requestId);
            if (request == null)
            {
                Console.WriteLine("Service request not found.");
                return;
            }

            request.IsCompleted = true;
            Console.WriteLine("Service request completed.");
        }

        public void ViewServiceRequests()
        {
            if (_requests.Any())
            {
                foreach (var request in _requests)
                {
                    Console.WriteLine($"\nService Request ID: {request.Id}");
                    Console.WriteLine($"Car: {request.Car.Make} {request.Car.Model} ({request.Car.LicensePlate})");
                    Console.WriteLine($"Request Date: {request.RequestDate:dd/MM/yyyy HH:mm:ss}");
                    Console.WriteLine($"Services:");
                    foreach (var service in request.Services)
                    {
                        Console.WriteLine($"- {service.Description} (Price: {service.Price:C})");
                    }
                    Console.WriteLine($"Completed: {request.IsCompleted}");
                }
            }
            else
            {
                Console.WriteLine("No service requests found.");
            }
        }
    }

    // Main program class
    public class Program
    {
        static void Main(string[] args)
        {
            // Create a ServiceManager instance
            var serviceManager = new ServiceManager();

            // Add some sample cars and services
            serviceManager.AddCar("Toyota", "Corolla", 2020, "ABC-123", "John Doe", "123-456-7890");
            serviceManager.AddCar("Honda", "Civic", 2019, "DEF-456", "Jane Smith", "456-789-0123");
            serviceManager.AddService("Oil Change", 50.00);
            serviceManager.AddService("Tire Rotation", 25.00);
            serviceManager.AddService("Brake Pad Replacement", 150.00);

            // Main program loop
            while (true)
            {
                Console.WriteLine("\nAutomated Car Service System");
                Console.WriteLine("1. Add Car");
                Console.WriteLine("2. Add Service");
                Console.WriteLine("3. Create Service Request");
                Console.WriteLine("4. Complete Service Request");
                Console.WriteLine("5. View Service Requests");
                Console.WriteLine("6. Exit");

                Console.Write("Enter your choice: ");
                int choice = int.Parse(Console.ReadLine());

                switch (choice)
                {
                    case 1:
                        Console.Write("Enter car make: ");
                        string make = Console.ReadLine();
                        Console.Write("Enter car model: ");
                        string model = Console.ReadLine();
                        Console.Write("Enter car year: ");
                        int year = int.Parse(Console.ReadLine());
                        Console.Write("Enter car license plate: ");
                        string licensePlate = Console.ReadLine();
                        Console.Write("Enter owner name: ");
                        string ownerName = Console.ReadLine();
                        Console.Write("Enter owner phone: ");
                        string ownerPhone = Console.ReadLine();
                        serviceManager.AddCar(make, model, year, licensePlate, ownerName, ownerPhone);
                        Console.WriteLine("Car added successfully.");
                        break;
                    case 2:
                        Console.Write("Enter service description: ");
                        string description = Console.ReadLine();
                        Console.Write("Enter service price: ");
                        double price = double.Parse(Console.ReadLine());
                        serviceManager.AddService(description, price);
                        Console.WriteLine("Service added successfully.");
                        break;
                    case 3:
                        Console.Write("Enter car ID: ");
                        int carId = int.Parse(Console.ReadLine());
                        Console.Write("Enter service IDs (comma-separated): ");
                        var serviceIds = Console.ReadLine().Split(',').Select(int.Parse).ToList();
                        serviceManager.CreateServiceRequest(carId, serviceIds);
                        break;
                    case 4:
                        Console.Write("Enter service request ID to complete: ");
                        int requestId = int.Parse(Console.ReadLine());
                        serviceManager.CompleteServiceRequest(requestId);
                        break;
                    case 5:
                        serviceManager.ViewServiceRequests();
                        break;
                    case 6:
                        Console.WriteLine("Exiting program.");
                        return;
                    default:
                        Console.WriteLine("Invalid choice. Please try again.");
                        break;
                }
            }
        }
    }
}
