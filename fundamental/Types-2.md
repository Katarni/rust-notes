# Типочки (generic)

## Traits

*Trait* - это что-то вроде интерфейса, но с особенностями
### Основы
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

#### trait как аргумент
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

#### trait как возвращаемое значение

чтобы вернуть тип, который имплементит *trait* достаточно следующих нехитрых действий
```rust
fn ret() -> impl Summary {}
```
*листинг 2.4*

К сожалению, код будет работать только если функция возвращает один тип (а не один из *N*)


