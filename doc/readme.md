Лабораторна робота № 13. Паралельне виконання. Multithreading.



Мета: Ознайомлення з моделлю потоків Java та організація паралельного виконання декількох частин програми.


1. ВИМОГИ

1. Розробник



- `Алієв Ахмед`;
- `студент групи КН-922г`;

1\.2 Загальне завдання

Використовуючи програми рішень попередніх задач, продемонструвати можливість паралельної обробки елементів контейнера: створити не менше трьох додаткових потоків, на яких викликати відповідні методи обробки контейнера.

Забезпечити можливість встановлення користувачем максимального часу виконання (таймаута) при закінченні якого обробка повинна припинятися незалежно від того знайдений кінцевий результат чи ні.

Для паралельної обробки використовувати алгоритми, що не змінюють початкову колекцію.

Кількість елементів контейнера повинна бути досить велика, складність алгоритмів обробки колекції повинна бути зіставна, а час виконання приблизно однаковий, наприклад:

- пошук мінімуму або максимуму;

- обчислення середнього значення або суми;

- підрахунок елементів, що задовольняють деякій умові;

- підбір за заданим критерієм;

- власний варіант, що відповідає обраній прикладної області.



1\.3 Індивідуальне завдання

5\.Довідник покупця. Знайти всі торгові точки, що працюють без вихідних та перерви на обід, що надають для консультації короткий телефонний номер та мають контактні телефони українських операторів мобільного зв'язку.

`         `2 ОПИС ПРОГРАМИ

Моя программа створює велику колекцію, далі у неї є таймаут, якщо робиться пошук цієї колекції та якщо таймаут закінчився, программа зупиняється та не робить дії які залишилось зробити.

2\.1 Засоби ООП

import java.beans.XMLDecoder;

import java.beans.XMLEncoder;

import java.io.\*;

import java.time.LocalDateTime;

import java.time.format.DateTimeFormatter;

import java.util.\*;

import java.util.concurrent.ExecutorService;

import java.util.concurrent.Executors;

import java.util.concurrent.TimeUnit;

import java.util.concurrent.atomic.AtomicInteger;

import java.util.regex.Pattern;

2\.2 Ієрархія та структура класів  

Має тількі один класс.



2\.3 Важливі фрагменти програми

public class Main {

public static class TradingPoint implements Serializable {

private String name;

private String address;

private List<String> phones;

private String specialization;

private LocalDateTime startTime;

private LocalDateTime endTime;

private int lunchBreak;

public int getLunchBreak() {

return lunchBreak;

}

public void setLunchBreak(int lunchBreak) {

this.lunchBreak = lunchBreak;

}

public String getName() {

return name;

}

public void setName(String name) {

this.name = name;

}

public String getAddress() {

return address;

}

public void setAddress(String address) {

this.address = address;

}

public List<String> getPhones() {

return phones;

}

public void setPhones(List<String> phones) {

this.phones = phones;

}

public String getSpecialization() {

return specialization;

}

public void setSpecialization(String specialization) {

this.specialization = specialization;

}

@Override

public String toString() {

return "TradingPoint{" +

"name='" + name + '\'' +

", address='" + address + '\'' +

", phones=" + phones +

", specialization='" + specialization + '\'' +

", work time= " + startTime.getDayOfWeek() + " - " + endTime.getDayOfWeek() +

startTime.format(DateTimeFormatter.ofPattern(" HH:mm ")) + "-" +

endTime.format(DateTimeFormatter.ofPattern(" HH:mm ")) +

", lunch break= " + lunchBreak + " minutes"

\+ '}';

}

public LocalDateTime getStartTime() {

return startTime;

}

public void setStartTime(LocalDateTime startTime) {

this.startTime = startTime;

}

public LocalDateTime getEndTime() {

return endTime;

}

public void setEndTime(LocalDateTime endTime) {

this.endTime = endTime;

}

public boolean validate() {

if (!name.matches("[A-Za-z\\s-]+")) {

return false;

}

if (!address.matches("[A-Za-z0-9\\s,]+")) {

return false;

}

for (String phone : phones) {

if (!phone.matches("\\d{10}|\\d{3}-\\d{3}-\\d{4}|\\d{4}")) {

return false;

}

}

if (!specialization.matches("[A-Za-z\\s]+")) {

return false;

}

return true;

}

public TradingPoint(String name, String address, List<String> phones,

String specialization, LocalDateTime startTime,

LocalDateTime endTime, int lunchBreak) {

this.name = name;

this.address = address;

this.phones = phones;

this.specialization = specialization;

this.startTime = startTime;

this.endTime = endTime;

this.lunchBreak = lunchBreak;

}

public TradingPoint() {

this.name = "no name";

this.address = "no address";

this.phones = List.of("no phones");

this.specialization = "no specialization";

this.startTime = LocalDateTime.now();

this.endTime = LocalDateTime.now();

this.lunchBreak = 0;

}

}


public static class LinkedListContainer<T> implements Iterable<T>, Serializable {

private Node<T> head;

private int size;

public LinkedListContainer() {

head = null;

size = 0;

}

public Node<T> getHead() {

return head;

}

public void setHead(Node<T> head) {

this.head = head;

}

public static class Node<T> implements Serializable {

private T data;

private Node<T> next;

public Node(T data) {

this.data = data;

next = null;

}

public Node() {

}

}

public int size() {

return size;

}

public boolean isEmpty() {

return size == 0;

}

public boolean contains(Object o) {

for (T item : this) {

if (item.equals(o)) {

return true;

}

}

return false;

}

public Iterator<T> iterator() {

return new LinkedListIterator();

}

class LinkedListIterator implements Iterator<T> {

private Node<T> current;

public LinkedListIterator() {

current = head;

}

@Override

public boolean hasNext() {

return current != null;

}

@Override

public T next() {

T data = current.data;

current = current.next;

return data;

}

@Override

public void remove() {

throw new UnsupportedOperationException();

}

}

public TradingPoint[] toArray() {

TradingPoint[] arr = new TradingPoint[size];

int i = 0;

for (T item : this) {

arr[i++] = (TradingPoint) item;

}

return arr;

}

public boolean add(T item) {

Node<T> newNode = new Node<>(item);

if (head == null) {

head = newNode;

} else {

Node<T> current = head;

while (current.next != null) {

current = current.next;

}

current.next = newNode;

}

size++;

return true;

}

public boolean remove(Object o) {

if (head == null) {

return false;

}

if (head.data.equals(o)) {

head = head.next;

size--;

return true;

}

Node<T> current = head;

while (current.next != null && !current.next.data.equals(o)) {

current = current.next;

}

if (current.next != null) {

current.next = current.next.next;

size--;

return true;

}

return false;

}

public void clear() {

head = null;

size = 0;

}

public void saveToFile(String fileName) {

try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(fileName))) {

oos.writeObject(this);

} catch (IOException e) {

e.printStackTrace();

}

}

public static LinkedListContainer<TradingPoint> loadFromFile(String fileName) {

try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(fileName))) {

return (LinkedListContainer<TradingPoint>) ois.readObject();

} catch (IOException | ClassNotFoundException e) {

e.printStackTrace();

}

return null;

}

public void saveToFileXml() {

try {

BufferedOutputStream fos = new BufferedOutputStream(new FileOutputStream("safe.xml"));

XMLEncoder encoder = new XMLEncoder(fos);

encoder.writeObject(this);

encoder.close();

fos.close();

} catch (IOException e) {

e.printStackTrace();

}

}

public static LinkedListContainer<TradingPoint> loadFromFileXml() {

try {

BufferedInputStream bis = new BufferedInputStream(new FileInputStream("safe.xml"));

XMLDecoder decoder = new XMLDecoder(bis);

LinkedListContainer<TradingPoint> appleCollection = (LinkedListContainer<TradingPoint>) decoder.readObject();

decoder.close();

bis.close();

return appleCollection;

} catch (IOException e) {

e.printStackTrace();

}

return null;

}


public void sortByTradingPoint(Comparator<TradingPoint> comparator) {

TradingPoint[] arr = this.toArray();

Arrays.sort(arr, comparator);

clear();

for (TradingPoint tp : arr) {

add((T) tp);

}

}

}

static TradingPoint point1 = new TradingPoint("someName", "someAddress", List.of("000-000-0000"), "someThing",

LocalDateTime.of(2004, 1, 1, 8, 30),

LocalDateTime.of(2004, 1, 1, 19, 30), 0);

static TradingPoint point2 = new TradingPoint("Cheese", "cheese", List.of("000-000-0000", "1234567891", "1234"), "cheese",

LocalDateTime.of(2002, 3, 1, 10, 30),

LocalDateTime.of(2003, 4, 5, 18, 0), 45);

static TradingPoint point3 = new TradingPoint("Apple", "yellow apple", List.of("0974567891"), "apples",

LocalDateTime.of(2000, 5, 1, 6, 30),

LocalDateTime.of(2022, 6, 5, 10, 45), 60);

private static void automatic(LinkedListContainer<TradingPoint> listContainer) {

for (TradingPoint tradingPoint : listContainer) {

if (hardWorkCheck(tradingPoint)) {

System.out.println("Hard work time: " + tradingPoint);

}

}

for (TradingPoint tradingPoint : listContainer) {

if (haveShortPhoneNumber(tradingPoint)) {

System.out.println("Short phone number: " + tradingPoint);

}

}

for (TradingPoint tradingPoint : listContainer) {

if (haveUkrainianPhoneNumber(tradingPoint)) {

System.out.println("Ukrainian phone number: " + tradingPoint);

}

}

}

public static boolean hardWorkCheck(TradingPoint tradingPoint) {

return (tradingPoint.getStartTime().getDayOfWeek().equals(tradingPoint.getEndTime().getDayOfWeek()))

&& tradingPoint.getLunchBreak() == 0;

}

public static boolean haveShortPhoneNumber(TradingPoint tradingPoint) {

String regex = "^\\d{4}$";

Pattern pattern = Pattern.compile(regex);

return tradingPoint.getPhones().stream().anyMatch(s -> pattern.matcher(s).matches());

}

public static boolean checkUkrainianPhoneNumber(String phone) {

String regex = "^(050|063|066|067|068|093|095|096|097|098|099)\\d{7}$";

return phone.matches(regex);

}

public static boolean haveUkrainianPhoneNumber(TradingPoint tradingPoint) {

return tradingPoint.getPhones().stream().anyMatch(Main::checkUkrainianPhoneNumber);

}

public static void main(String[] args) throws InterruptedException {

LinkedListContainer<TradingPoint> listContainer = new LinkedListContainer<>();

TradingPoint point4 = new TradingPoint("Orange", "fresh orange", List.of("0912345678"), "oranges",

LocalDateTime.of(2003, 9, 4, 10, 0),

LocalDateTime.of(2022, 2, 14, 13, 0), 50);

TradingPoint point5 = new TradingPoint("Watermelon", "sweet watermelon", List.of("0943215678"), "watermelons",

LocalDateTime.of(2004, 10, 5, 11, 30),

LocalDateTime.of(2022, 1, 31, 16, 15), 35);

TradingPoint point6 = new TradingPoint("Mango", "ripe mango", List.of("0923456712"), "mangoes",

LocalDateTime.of(2005, 11, 6, 12, 0),

LocalDateTime.of(2022, 12, 15, 9, 0), 40);

TradingPoint point7 = new TradingPoint("Pineapple", "juicy pineapple", List.of("0967891234"), "pineapples",

LocalDateTime.of(2006, 12, 7, 13, 30),

LocalDateTime.of(2022, 11, 28, 12, 30), 25);

TradingPoint point8 = new TradingPoint("Pear", "sweet pear", List.of("0987123456"), "pears",

LocalDateTime.of(2007, 1, 8, 14, 0),

LocalDateTime.of(2022, 10, 18, 15, 45), 55);

TradingPoint point9 = new TradingPoint("Banana", "ripe banana", List.of("0932154768"), "bananas",

LocalDateTime.of(2001, 7, 2, 8, 0),

LocalDateTime.of(2022, 4, 12, 11, 15), 45);

TradingPoint point10 = new TradingPoint("Grapes", "red grapes", List.of("0987654321"), "grapes",

LocalDateTime.of(2002, 8, 3, 9, 30),

LocalDateTime.of(2022, 3, 21, 14, 30), 30);

TradingPoint point11 = new TradingPoint("Apple1", "yellow apple", List.of("0974567891"), "apples",

LocalDateTime.of(2000, 5, 1, 6, 30),

LocalDateTime.of(2022, 6, 5, 10, 45), 60);

listContainer.add(point1);

listContainer.add(point2);

listContainer.add(point3);

listContainer.add(point4);

listContainer.add(point5);

listContainer.add(point6);

listContainer.add(point7);

listContainer.add(point8);

listContainer.add(point9);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point10);

listContainer.add(point11);

listContainer.add(point11);

listContainer.add(point11);

listContainer.add(point11);

listContainer.add(point11);

listContainer.add(point11);

listContainer.add(point11);

listContainer.add(point11);

listContainer.add(point11);

listContainer.add(point11);

TradingPoint[] numbers = listContainer.toArray();

System.out.println("Current count of array: " + numbers.length);

Scanner myObj = new Scanner(System.in);

System.out.println("Enter timeout");

String userName = myObj.nextLine();

int timeout = Integer.parseInt(userName);


ExecutorService executor = Executors.newFixedThreadPool(numbers.length);

boolean[] interrupted = new boolean[numbers.length];

for (int i = 0; i < numbers.length; i++) {

final int index = i;

executor.execute(() -> {

try {

if (haveUkrainianPhoneNumber(numbers[index])) {

System.out.print(" ");

}

if (Thread.currentThread().isInterrupted()) {

interrupted[index] = true;

return;

}

System.out.println("Parallel task " + index + " ");

} catch (Exception e) {

interrupted[index] = true;

}

});

}

executor.shutdown();

if (!executor.awaitTermination(timeout, TimeUnit.MILLISECONDS)) {

for (int i = 0; i < numbers.length; i++) {

if (!interrupted[i]) {

executor.shutdownNow();

System.out.println("Timeout expired, some tasks may not have completed");

break;

}

}

}

}

}



`      `3 ВАРІАНТИ ВИКОРИСТАННЯ

- дебагер lldb;
- консоль;
- дебагер gdb;





ВИСНОВОК

Як висновок можу сказати, що при виконанні даної лабораторної роботи було набуто практичного досвіду з моделлю потоків Java та організацією паралельного виконання декількох частин програми, та в мене вийшло зробити своє завдання.
