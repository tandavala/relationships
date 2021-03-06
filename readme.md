# Relationships

This package adds two more kind of relationships based on Laravel's original from *Has Many Through*

- [Installation](#installation)
- [Has One Through Several](#has-one-through-several)
- [Has Many Through Several](#has-many-through-several)
- [How To Use](#how-to-use)

## Installation

In the **require** key of **composer.json** file add the following:

```php
"resultsystems/relationships": "~0.4.0"
```

**Important:** Do not use `dev-master`. Instead, use the tagged version, like shown before.

Run the Composer **update** comand:

```php
composer update
```
or

```php
composer require resultsystems/relationships
```

 <a name="has-one-through-several"></a>
## Has One Through Several


 - Similar to Laravel's hasOne

The "has-one-through-several" relationship provides a convenient shortcut for accessing distant relations via an intermediate relation. For example, a `Frequency` model might have one `Subject` model through the intermediates `Skill` and `Schedule` model. In this example, you could easily gather subject for a given frequency. Let's look at the tables required to define this relationship:

    frequencies
        id - integer
        schedule_id - integer
        date - date

    skills
        id - integer
        teacher_id - integer
        subject_id - integer
        title - string

    schedules
        id - integer
        group_id - integer
        skill_id - integer
        name - string

    subjects
        id - integer
        name - string

        // model = frequency
        // frequency.schedule_id = schedules.id
        // schedules.skill_id = skills.id
        // skills.subject_id = subjects.id

```php
<?php

namespace App;

use ResultSystems\Relationships\Model;

class Frequency extends Model
{
    public function subject()
    {
        // You can add several model in array

        return $this->hasOneThroughSeveral([
            Subject::class,
            Skill::class,
            Schedule::class,
        ], null, null); // null -> optional (foreignKey -> schedule_id, localKey -> id)
    }

    // or
    public function subject()
    {
        // You can add several model in array

        return $this->hasOneThroughSeveral([
            Subject::class => [
                'subjects.id' => 'skills.subject_id',
            ],
            Skill::class => [
                'skills.id' => 'schedules.skill_id',
            ],
            Schedule::class => [
                'schedules.id' => 'frequencies.schedule_id',
            ],
        ], null, null); // null -> optional foreignKey -> schedule_id, localKey -> id)
    }
}
```

 <a name="has-many-through-several"></a>
## Has Many Through Several

 - Similar to Laravel's hasMany

The "has-many-through-several" relationship provides a convenient shortcut for accessing distant relations via an intermediate relation. For example, a `Group` model might have many `Teacher` models through the intermediates `Schedule` and `Skill` model. In this example, you could easily gather all teachers for a given group. Let's look at the tables required to define this relationship:

    groups
        id - integer
        name - string

    teachers
        id - integer
        name - string

    schedules
        id - integer
        group_id - integer
        skill_id - integer
        name - string

    skills
        id - integer
        teacher_id - integer
        subject_id - integer
        title - string

        // model = group
        // groups.id = schedules.group_id
        // skills.id = schedules.skill_id
        // teachers.id = skills.teacher_id

```php
<?php

namespace App;

use ResultSystems\Relationships\Model;

class Group extends Model
{
    public function teachers()
    {
        return $this->hasManyThroughSeveral([
           Teacher::class,
           Skill::class,
           Schedule::class => [
                'schedules.group_id' => 'groups.id',
            ],
        ]);
    }
}
```

<a name="how-to-use"></a>
## How To Use

### Mode 1

```php
<?php

namespace App;

use ResultSystems\Relationships\Model;

class Group extends Model
{
    public function subjects()
    {
        return $this->hasManyThroughSeveral([
            Subject::class,
            Skill::class,
            Schedule::class => [
                'schedules.group_id' => 'groups.id',
            ],
        ]);
    }
}
```

### Mode 2

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use ResultSystems\Relationships\Traits\RelationshipsTrait;

class Frequency extends Model
{
    use RelationshipsTrait;

    public function subject()
    {
        // You can add several model in array

        return $this->hasOneThroughSeveral([
            Subject::class,
            Skill::class,
            Schedule::class,
        ], null, null); // null -> option (foreignKey -> schedule_id, localKey -> id)
    }
}
```
