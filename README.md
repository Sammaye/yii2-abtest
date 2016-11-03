# yii2-abtest
A simple extension for writing A/B Tests in Yii2.

This extension does not include the hooks for various programs, but does allow you to `list()` so you can quickly 
add it to your analytics code.

## Installing

Simply include it from composer:

    php ./composer.phar require sammaye/yii2-abtest:"@stable"

## Adding it to your configuration

Easiest thing is to just show an example of my configuration:

	'test' => [
		'class' => 'sammaye\abtest\Test',
		'filter' => [
			'rules' => [
				[
					'allow' => false,
					'roles' => ['staff']
				]
			]
		],
		'tests' => [
			[
				'name' => 'Beta Search',
				'values' => ['old', 'new'],
				'default' => 'new'
			]
		]
	],
	
The configuration breaks down into two parameters:- `filter` and `tests`.

**Only `tests` is required.**

`filter` allows you to use the `AccessControl` like you would on any controller and 
provide a set of rules whereby the tests should not take effect.

When an `'allow' => false` filter is matched the test will return the `default` parameter from the test you are looking at 
and will not not record it in `$_SESSION`.

This way, when you come to `list()` at the end of the page to push onwards for analytics, these tests **will not appear** in that list.

## Using it

Once it is fully configured you can just use it to detect which path a user takes:

    Yii::$app->test->value('Beta Search')
    
This function will return either `old` or `new` depending on whether I am a staff member or lady random chooses me.

In order to add it to your analytics code you need to list all active tests and their values:

    Yii::$app->test->list()
    
This will then print out a list of (for me):

    [
        'Old Search' => [
        	'name' => 'Old Search',
        	'value' => 'old' // active
        	// custom data could be housed 
        	// here from the configuration like "goal"
        ]
    ]

So, the key is name of the test and the value is the configuration object but with `values` replaced with only the active `value`.