import pytest
# --- Заглушка бази даних ---
class MockDatabase:
    def __init__(self):
        self.cars = {}
        self.next_id = 1
    def add_car(self, car_data):
        car_id = self.next_id
        self.next_id += 1
        self.cars[car_id] = {**car_data, "id": car_id}
        return car_id
    def get_car(self, car_id):
        return self.cars.get(car_id)
    def get_all_cars(self):
        return list(self.cars.values())
    def update_car(self, car_id, updates):
        if car_id in self.cars:
            self.cars[car_id].update(updates)
            return True
        return False
    def delete_car(self, car_id):
        return self.cars.pop(car_id, None) is not None
# --- Функції API (приклад) ---
db = MockDatabase()
def addCar(car_data):
    if not all(k in car_data for k in ("make", "model", "year", "price")):
        return {"success": False, "error": "Missing fields"}
    car_id = db.add_car(car_data)
    return {"success": True, "carId": car_id}
def getCars():
    return db.get_all_cars()
def updateCar(car_id, updates):
    success = db.update_car(car_id, updates)
    return {"success": success}
def deleteCar(car_id):
    success = db.delete_car(car_id)
    return {"success": success}
# --- Юніт-тести ---
def test_add_car_success():
    car_data = {"make": "Toyota", "model": "Corolla", "year": 2020, "price": 20000}
    result = addCar(car_data)
    assert result["success"] is True
    car = db.get_car(result["carId"])
    assert car["model"] == "Corolla"
    assert car["price"] == 20000
def test_add_car_missing_field():
    car_data = {"make": "Toyota", "model": "Corolla", "year": 2020}  # price відсутній
    result = addCar(car_data)
    assert result["success"] is False
    assert "Missing fields" in result["error"]
def test_get_cars():
    db.cars.clear()  # очищаємо базу перед тестом
    addCar({"make": "Honda", "model": "Civic", "year": 2019, "price": 18000})
    addCar({"make": "Ford", "model": "Focus", "year": 2018, "price": 15000})
    cars = getCars()
    assert len(cars) == 2
    assert all("make" in car and "model" in car for car in cars)
def test_update_car_success():
    db.cars.clear()
    car_id = addCar({"make": "BMW", "model": "X5", "year": 2021, "price": 50000})["carId"]
    result = updateCar(car_id, {"price": 52000})
    assert result["success"] is True
    car = db.get_car(car_id)
    assert car["price"] == 52000
    assert car["make"] == "BMW"
def test_update_car_not_exist():
    db.cars.clear()
    result = updateCar(999, {"price": 10000})
    assert result["success"] is False
def test_delete_car_success():
    db.cars.clear()
    car_id = addCar({"make": "Audi", "model": "A4", "year": 2020, "price": 35000})["carId"]
    result = deleteCar(car_id)
    assert result["success"] is True
    assert db.get_car(car_id) is None
def test_delete_car_not_exist():
    db.cars.clear()
    result = deleteCar(999)
    assert result["success"] is False
