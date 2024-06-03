# Quick Reference - AED Cheat Sheet

This is a quick reference, please read the entire guide for more details.

**This is the bare minimum to improve the performance of untrained coding assistants**

If you're a Rails developer, or come from a similarly structured framework, you'll find a lot of this inspired by the Rails conventions.


# Naming Conventions


## General Principles & Guidelines

- Avoid unnecessary jargon or slang.
- Never create a whole new lexicon for your code base by applying a theme.
	- Code names for code bases are entirely acceptable and expected.
- Names that read like plain English are preferred


## Object & Attribute Naming Conventions

- Data models should be singular, and only concerned with their own individual behavior.
	- Good: `Customer`
	- Bad: `Customers`
- Classes should be name spaced according to the feature that is being implemented.
	- Good: `Billing::ActivateNewCustomerSubscription`
	- Bad: `NewCustomerSubscription`
- Class names should be short statements or phrases that clearly express the process being performed.
	- Good: `PerformCustomerAccountLocking`
	- Bad: `LockCustomers`
- Class attributes of non-enumerable primitive types should be phrased as short statements for what the intended purpose of the attribute is.
	- Example class `Customer`
	- Well named attribute:  `first_name` or `full_name`
	- Poorly named attribute: `name`
- Class attributes of enumerable (Array or Array-like) objects or types should be phrased with `list_of_` or `collection_of_` or `array_of_`
	- Example class `Customer` has many `Order`s
	- Well named attribute: `list_of_previous_orders` or `collection_of_previous_orders` or `array_of_previous_orders`
	- Poorly named attribute: `orders` or `previous_orders`
	- **Suggestion**: in dynamically typed languages, adding the object type to the end of the name can be very helpful by using the `_as_` phrasing in your naming
		- Example: `list_of_previous_orders_as_hashes`
- Class attributes of non-primitive types should be named using short statements or phrases that accurately and clearly express how that attribute is to be used
	- Example class: `Customer` with a `Subscription`
	- Ideal: `currently_active_subscription`
	- Acceptable: `active_subscription`
	- Bad: `subscription`
- Class attributes for boolean types should be named as if the expression is a question beginning with `if`
	- Example class: `Customer`
	- Good: `has_a_valid_payment_method`
	- Bad: `payment_method_present`


## Method Naming Conventions

- Method names should be phrases or statements that explain the process thats taking place
	- When possible, include wording to describe the expected return type
- Method parameters should be named when possible
	- Ideally as if reading a plain statement


## File & Folder Naming Conventions

These primarily apply to domain/business logic. Use your appropriate framework folder structure where necessary. These conventions are primarily if you are using an agent helper that is trained to work with more than 1 file at a time or perform file edits/updates autonomously.

- The file name should be a lower snake case of the primary class from the file.
	- Class name `ProcessCustomersWithExpiredSubscriptions`
	- Filename: `process_customers_with_expired_subscriptions.cr`
- Classes that are name spaced should be in folders named for that namespace
	- Class name `Billing::ProcessCustomersWithExpiredSubscriptions`
	- Filename: `billing/process_customers_with_expired_subscriptions.cr`
- Data models rarely need to be name spaced. 
	- Reserve this kind of naming for single-table inheritance or other very specific situations where you need a data model to be name spaced.



Example
```crystal
# File found under `billing/process_customers_with_expired_payment_methods`
class Billing::ProcessCustomersWithExpiredPaymentMethods
  property collection_of_customers_that_have_expired_payment_methods : Array(Customer) = [] of Customer
  property collection_of_customers_that_were_retried_and_failed : Array(Customer) = [] of Customer
  property all_of_the_customers_have_been_processed : Bool = false

  def initialize(@collection_of_customers_that_have_expired_payment_methods)
  end

  def perform
    retry_customers_who_failed_payment_processing_with_an_expired_card
    mark_customer_accounts_as_delinquent_and_prevent_further_use
  end

  private def retry_customers_who_failed_payment_processing_with_an_expired_card
    # Your business logic goes here
  end

  private def mark_customer_accounts_as_delinquent_and_prevent_further_use
    # Your business logic goes here
  end

end
```

## Process Manager Conventions

Process managers are the starting point of your businesses internal domain-specific language (DSL).

These objects are _typically_ just a plain class that is not part of a specific framework. Many frameworks have some utilities that bleed out of the framework and into your business logic for common usecase tasks.

A more formal definition that encompasses what the spirit of the process manager is:

`Process Manager: a starting point in a business process where a workflow of one or more steps begins and ends, with the final product being the end of the computational process for the business.`

Process managers conform to the following:
	- The `initialize` method receives all of the necessary information possible to perform the process
		- Any necessary data organization should happen during the objects initialization step
	- The entry point method `perform` is defined, and performs all of the methods necessary for the business task to be completed in a single method call
		- A well written `perform` method will read almost like psuedo code when outlining each step that's being performed.
	- Use read-only public accessor methods if the object is going to be used for anything other than returning a single result

