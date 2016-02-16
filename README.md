# Database Schema Naming Convention - DSNC - DaSoNiC - Dasonic

### Spec Version: 0000

- Can version this Spec, so that as changes are made, the exact version of the spec can be specified so that there is clarity of intention.

- No plurals.  Ever.  Never.  Never, ever.
	- Developers will have to remember or look-up whether this data was referred to in singular or plural form.
	- Some words change spelling significantly from singular to plural, and this is more true in languages other than english where many words do not become plural by just adding an "s" to them (or other deterministic character change).  Example:  "company", "companies".  Some words do not have well-known plural names, and might have unknown spellings:  "squid", "squidii" ; "dolphin", "pod"
	- To make this easy on yourself, just change your thinking to always thinking of a reference, which could be singular or plural.  Then when you internally think about this data, you know it does not matter which it is, and so you do not need to have this label ever be written in the plural.  It is based on context as to whether there is only 1 or more than 1 possible results to the value of the label.
		- Example:  
			- company: a table of companies
			- company_employee: employees in a company
			- company_employee_contact: Value = email, cell phone, etc. Type determines how value is used (connect to email, connect to phone call or text message, etc)
			- contact_type: email, cell phone, etc.  Determines how a value will be used for a given contact.  Many tables might need contact information.  Examples:  company_employee, vendor_employee, customer_employee, etc.  (In this case all of these could be consolidated to the "company" table, and given a type table lookup for "internal", "vendor" and "customer", but perhaps a more strong security measure is to just separate this data into different tables, or for scalability as each will have a different size of active data in production.  To combine them into a single table or split them into separate table has 2 different engineering results, so they should be chosen to match the desired results.  This isnt Dasonic style, it's project specific, but I will reference things like this in the Dasonic information, since they are relevant design decisions and visible in certain places when describing Dasonic, so good as a jumping off point to discuss them.

- More problems with plurals:
	- users
	- companies
		- companies_user   ...or...  company_user  ???  ...or... companies_users  ???
			- Ugly.  Too easy to get it wrong.  If it's going to be consistent it should be all plural or all singular.  All plural is messier, so choose singular and never deviate from it and reap the benefits of the simplicity.

		- This is a lot like capitalization.  You can go all caps with underscores, all caps together, camel case, case case with first letter lower, all lower case with underscores, or all lower case together.  These are the basic options of capitalization of words to create a label, or variable name, or field name.
			- Since using separate naming methodolgies for different types of data provides a more complex, but still easily comprehensible, method of visualizing things, this means we can mix and match in total, but for a specific type of data, it should be rigid and consistent.  My method is:

			- Classes and Functions:  CamelCaseFromStart
			- Variables and Schema Table/Field names: lower_case_with_underscores
			- Global variables: UPPER_CASE_WITH_UNDERSCORES

			- This is easy to apply, but clearly has differences between types.  
				- Classes and Method/Functions are the same because there are more Methods/Functions than Classes (at least 1:1), so I optimize for the Methods/Functions, and Classes are not really so special as to need their own format IMO, since they are rarely accessed in a way that is not obviously the accessing of a Class.
					- I prefer not to use the lowerCaseFirstLetterCamelCase method as it is not able to be differentiated from the lower_case_underscores method of variables if there is only one word.  Example:  user  != user.  With CamelCase, it would be User != user, which is obviously visible.   This provides more consistency and comprehensibility at a glance.
				- Variables are differentiated easily from classes, methods and functions by starting with a lower case letter, which is the primary thing to differentiate immediately, as beyond keywords and builtins, these are the majority of your labels.
				- Where the language differs from these standards, go along with the language, and comply with these standards where not using or overloading the base language formats.


- Use default tense, unless you prefix it with a special prefix term that indicated why this tense is different:
	- "is_active":  boolean, is currently active?  "is_" prefix always means Boolean value (0=false, 1=true, null=Unknown).  Always in present tense, as it is a "current tracking" type term ("is_")
	- "was_activated", "was_" prefix without a suffix of the data type is Boolean, (0=false, 1=true, null=Unknown).  Uses past-tense verbs, which matches "was" tense.
	- "was_activated_time", using suffixes to track different data.  This would be an example of a single-data point for this "was_" prefixed flag data.
	- "was_activated_start_time" and "was_activated_stop_time" 

- List of common phases to use, when matching variables.  This means you dont make up new terms when you could use these instead, to provide a common terminology set:
	- "_start_", "_stop_".  Always use these instead of "begin/end", "start/finish", or any other similar words or combinations.  Even if they are similar, but you feel slightly different, use these instead.  If you need more granularity, use another hierarchical term before this, such as:  "was_activated_phase_0_start_time", "was_activated_phase_0_stop_time", which allows another axis of scaling, with obvious ordering capabilities.  Can use term like "_phase_00_" or  "_phase_000_" is large numbers of terms are expected.  This is not saying that adding in tons of columns into a database table is a great idea, and it may not be a good idea, but if you have to do it because of your goals and engineering limitations/requirements, then you should do it with the most comprehensible and scalable naming convention you can.

- Databases are hierarchies of Name Spaces, and so should be treated as a Name Spacing system.  Strict adherence to name spacing rules that are internally consistent can allow for maximum growth inside the name space, with maximum comprehensibility, especially at large scale.  
	- Small databases may be comprehensible without rigid structure on their table and field names, but once hundred or thousands of tables exist, any deviation in a standardized name space creates large comprehensibility problems, moving into completely non-comprehensible except by domain experts very quickly.  Many schemas are essentially not comprehensible by many people on development teams, whose job is to work with the data in those schemas, because of the non-comformity of the naming and data conventions in those schemas.  Mostly, it is the naming, but perfoming odd or unique data formatting or structuring is a secondary issue.

- Compose field names 
	- Prefix with topic, go from more specific to less specific.  Prefixes are like directory structures.
	- Think of them as a series of labels, separated by underscores
	- Underscores are also used to separate internal terms in the label such as "web_site", it is a "Web Site" and could be called "website", but having the separate name allows for a common usage such as "web_widget", which is not directly related to a "web_site", but they share the common prefix of "web_" which makes them organize very well in alphabetical sorted listing, and provides an immediate visual comprehension that they are related, and yet are not in the same hierarchy.  
		- Ex: web_widget is not underneath web_site, such as web_site_widget would be, if it existed.  web_widget stands alone, and is independent of web_site, where is "web_site_widget" existed, then it would be "web_site" specific, as it is under the hierarchical prefix of "web_site_", which tells us that it will reference a "web_site" and be "web_site" specific data.  This gives us many pieces of information about this table, just because of how the name was created.  
		- Rigid adherence to this naming convention allows us to immediately understand the use of this table, no matter how many tables we have (100s or 1000s) we can immediately tell what any table does in relation to it's parents in it's name space hierarchy.  Compare this to tables that do not adhere to this type of naming convention, and you will see what information is missing that could have been provided in an easy-to-read naming convention.

	- All parts of the names are separated by underscores.  You can compare them visually to see what the prefixes are, it's pretty obvious when you look at them, which is additional visual comprehension information that is not available if you dont use a technique that provides this data, such as this one.


- Flag Booleans are match tense, in English, using limited prefix tests:  "is_", "was_"
	- Ex: "is_active", "was_activated"

- Tracking a bunch of different things in fields can benefit from common prefixes, suffixes, like:
	- Ex: "deployment_created_time", "deployment_approved_time", "deployment_deployed_time, "deployment_started_time", "deployment_finished_time"

	- Notice "deployment" is a noun, it is the topic being described.  "deploy" is a verb, which I originally chose, and then decided the deployment was the real over-all topic here, and "deploy" was actually more accurately a stage of deployment, which can be seen by the "deployment_deployed_time" variable.

	- Notice also the matching tenses between all the different terms:  approved/deployed/started/finished, are all in the past-tense, so they agree with each other's tenses.  When this agreement exists, then the developers who are using this schema can think about each stage in the same manner, providing more consistency, and when they go to write new code, they do not need to guess or look-up what the field names are, if they know the stages, then they know the field name from the convention, because it is in the deployment "approval" stage, so it is "approved" and in complete "deployment_approved_time".  Also with "_time" always being "DATETIME" style field type, then they always know what to call this.

- Some field types, like "DATETIME" style types should always be suffixed with a representative label, such as "_time", or "_date" for the "DATE" style types.  This allows immediate visual recognition of this type, and provides a name-spacing rule so that more fields can be created without causing name-conflicts.  When you are tracking information about an event, you may start thinking about tracking different parts of that data initially, and later need to add more data.  If you are always creating your schema in this rigid fashion, where convention dictates the names, then you will not hit as many naming conflicts because you are making them more-unique just by convention.
	- Example:  "created_time" as a "DATETIME" style field name, instead of "created", this tells you more specifically what type of data this is.  Then if you have "created_user_id", you have a standardize prefix ("created_") that can have hierarchical suffixes added to it, which tell you many pieces of information, such as how those fields are related, without having to look up documentation or remember all the relational details of the database.  This is especially important as databases change over time, since it is done by different people with different goals, so the naming convention is likely to change wildly over this time unless strict rules are created and adhered to.

- These can then be matched to other fields that match the prefixes, but have different suffixes, telling you their different types.  Be consistent with types ("_time" = DATETIME())
	- Ex: "deployment_created_user_id", "deployment_approved_user_id", "deployment_deployed_user_id", which would reference the "user.id" table value, and is being matched to the event at "deploy_created_time", etc.  This gives us direct visual mapping between the foreign keys, etc.

- Dont be afraid of long names.  This is all the context you will ever have about this data, except for documentation which you can be sure will not be as up-to-date as the schema itself.  Be self-documenting and do not choose short names for the data.  
	- Only in situations like Column Store or Document databases where the column names are included in the data storage, and so need to be short.  In these cases, make a descriptive name map, and use them as lookups, so the field names are just short hashes and can be maximally unique without human reference being implied.  Always perform lookups on these names.

- Make your own rules up any time there is a new case, and ensure those rules are followed just as much as any pre-existing rules.  The important part of Dasonic is not that these are the best rules, but that these rules are internally consistent, and lead to a more comprehensible, usable and scalable database schema.  You can replace these rules with any of your own rules and receive those differing benefits from how your rules fit together.

	- Any place where you find you dont know how to apply the current rules, just create a new rule, while trying to design how the consequences of that rule being used in all situations will effect the overall schema structures, and any downstream rules that use this rule as a basis.  The rules I am presenting were designed and refined in this manner, so if you do the same things, your rules should similarly become well-integrated and take many different things into account, while remaining fairly simple and composable.  Rules need time for adjustment to become very general, so feel free to make adjustments to any of my rules as well as new ones you add, because making changes and evaluating the results is what gives a deeper understanding of how the rules really work, and how to make them work for you better, and how to improve them more over time.

- Reserved field names:
	- id: primary key, int, auto_increment (usually)
	- parent: int, foreign key to same table.id.  How to have hierarchies.

