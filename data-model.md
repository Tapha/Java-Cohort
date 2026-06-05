* Users - ID(Primary key), First name, Last name, email, password, Created at, Updated at, Deleted at
* Meals - ID(Primary key), UserID(Foreign Key), RecipieID(Foreign Key), Name, Created at, Updated at, Deleted at
* Fridge - ID(Primary key),Created at, Updated at, Deleted at
* Images -ID(Primary key), FridgeID(Foreign Key), MealID(Foreign Key), UserId(Foreign Key), Created at, Updated at, Deleted at
* Ingredients-ID(Primary key), FridgeID(Foreign Key), imageID(Foreign Key), Name, Created at, Updated at, Deleted at
* Recipies - ID(Primary key), Name, includes time to complete column, Created at, Updated at, Deleted at
* Preferences - ID(Primary key), preference type, preference description,Created at, Updated at, Deleted at
* UserPreferences - Id(Primary key),UserId, PreferencesID,Created at, Updated at, Deleted at
* IngredientRecipie = ID(Primary Key) , ingredientid, recipieid, portion, measurementType,Created at, Updated at, Deleted at
