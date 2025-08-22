# Типочки (trait)

*Trait* - это что-то вроде интерфейса, но с особенностями
## Основы
Вот простой пример *trait*
```rust
pub trait Summary {
	fn summarize(&self) -> String;
	
	fn another_fn(&self) -> String {
		String::from("default")
	}
}

pub struct SocialPost {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub repost: bool,
}

impl Summary for SocialPost {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```
*листинг 2.0*

Ничего сложного

### trait как аргумент
Можно задать функцию, которая будет принимать любой тип, что имплементит trait

```rust
fn notify(item: &impl Summary) {}

// или

fn notify<T: Summary>(item: &T) {}
```
*листинг 2.1*

Если нужно указать два обязательных *trait*, пишется вот так
```rust
fn notify(item: &(impl Summary + Display)) {}

// или

fn notify<T: Summary + Display>(item: &T) {}
```
*листинг 2.2*

Можно вынести все *trait* в конец с *where*

```rust
fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{
```
*листинг 2.3*

### trait как возвращаемое значение

чтобы вернуть тип, который имплементит *trait* достаточно следующих нехитрых действий
```rust
fn ret() -> impl Summary {}
```
*листинг 2.4*

К сожалению, код будет работать только если функция возвращает один тип (а не один из *N*)

## Интересненькое
Думаю очев, что компилятор просто генерит кучу кода для всех имплементаций *trait* (и не только), что вы использовали

*monomorphization*(страшна) - процесс генерации кода для всех *trait*.
Все (всели) оптимизации компилятора делаются после того, как весь код заимплементен и больше нет generic кода.

### dynamic dispatch

Вместо того, чтобы знать, что за тип нам придет с *trait*, мы можем не знать. Для этого используется *&dyn*. Когда мы знаем тип, мы знаем, где лежит сгенеренная функция, когда мы используем *dynamic dispatch*, нам нужна *виртуальная таблица функций* (*vtable*) 

Выглядеть это будет как-то так

```rust
impl String {
	pub fn contains(self&, p: &dyn Pattern) -> bool {}
}
```
*листинг 2.5*

*trait object* - комбинация типа *trait* и *vtable*

*Trait object* всегда !Sized (те его размер не известен) => если мы хотим, чтобы наш *trait* никогда не был dynamic, просто скажите, что он Self: Sized

Dynamic dispatch не нуждается в генерации тонны кода и будет делать все через *vtable* Это уменьшение времени компиляции, но и минус в оптимизации под разные типы.