---
layout: api
pill: hooks
subtitle: API hooks
---
# API: hooks

There are three ways you can hook into the Flyway API.

## Java-based Migrations

The first one is the the most common one: [Java-based Migrations](/documentation/migrations#java-based-migrations)
when you need more power than SQL can offer you. This is great to for dealing with LOBs or performing advanced
data transformations.

In order to be picked up by Flyway, Java-based Migrations must implement the
[`JavaMigration`](/documentation/api/javadoc/org/flywaydb/core/api/migration/JavaMigration) interface. Most users
however should inherit from the convenience class [`BaseJavaMigration`](/documentation/api/javadoc/org/flywaydb/core/api/migration/BaseJavaMigration)
instead as it encourages Flyway's default naming convention, enabling Flyway to automatically extract the version and
the description from the class name.

## Java-based Callbacks

Building upon that are the Java-based [Callbacks](/documentation/callbacks)
when you need more power or flexibility in a Callback than SQL can offer you.

They can be created by implementing the [**Callback**](/documentation/api/javadoc/org/flywaydb/core/api/callback/Callback)
interface:

```java
package org.flywaydb.core.api.callback;

/**
 * This is the main callback interface that should be implemented to handle Flyway lifecycle events.
 */
public interface Callback {
    /**
     * Whether this callback supports this event or not. This is primarily meant as a way to optimize event 
     * handling by avoiding unnecessary connection state setups for events that will not be handled anyway.
     *
     * @param event   The event to check.
     * @param context The context for this event.
     * @return {@code true} if it can be handled, {@code false} if not.
     */
    boolean supports(Event event, Context context);

    /**
     * Whether this event can be handled in a transaction or whether it must be handled outside a transaction
     * instead. In the vast majority of the cases the answer will be {@code true}. Only in the rare cases where
     * non-transactional statements are executed should this return {@code false}.
     * This method is called before {@link #handle(Event, Context)} in order to determine in advance whether a 
     * transaction can be used or not.
     *
     * @param event   The event to check.
     * @param context The context for this event.
     * @return {@code true} if it can be handled within a transaction (almost all cases). {@code false} if it
     * must be handled outside a transaction instead (very rare).
     */
    boolean canHandleInTransaction(Event event, Context context);

    /**
     * Handles this Flyway lifecycle event.
     *
     * @param event   The event to handle.
     * @param context The context for this event.
     */
    void handle(Event event, Context context);
}
```

The `event` argument tells you which [`Event`](/documentation/api/javadoc/org/flywaydb/core/api/callback/Event) 
(`beforeClean`, `afterMigrate`, ...) is being handled and the `context` argument gives you access to things
like the database connection and the Flyway configuration.

## Custom Migration resolvers &amp; executors

For those that need more than what the SQL and Java-based migrations offer, you also have the possibility to
implement your own [`MigrationResolver`](/documentation/api/javadoc/org/flywaydb/core/api/resolver/MigrationResolver)
coupled with a custom [`MigrationExecutor`](/documentation/api/javadoc/org/flywaydb/core/api/executor/MigrationExecutor).

These can then be used for loading things like CSV-based migrations or other custom formats.

By using the `skipDefaultResolvers` property, these custom resolvers can also be used
to completely replace the built-in ones (by default, custom resolvers will run in addition to
built-in ones).

<p class="next-steps">
    <a class="btn btn-primary" href="/documentation/api/javadoc">API: Javadoc <i class="fa fa-arrow-right"></i></a>
</p>