# NAME

Moo::Role::ToJSON - a Moo role for a TO\_JSON method

# SYNOPSIS

    package My::Message;
    use Moo;
    with 'Moo::Role::ToJSON';

    has feel_like_sharing => (is => 'rw', default => 0);
    has message => (is => 'ro', default => 'Hi Mum!');
    has secret  => (is => 'ro', default => 'I do not like eating healthily');

    sub _build_serializable_attributes { [qw/message secret/] }

    # optional instance method to selectively serialize an attribute
    sub is_attribute_serializable {
        my ($self, $attr) = @_;

        if ($attr eq 'secret' && !$self->feel_like_sharing) {
            # returning a false value won't include attribute when serializing
            return 0;
        }

        return 1;
    }

    1;

    # t/test.t
    use Test2::Bundle::More;
    use Test2::Tools::Compare;

    my $message = My::Message->new();
    is $message->TO_JSON => {message => 'Hi Mum!'};

    $message->feel_like_sharing(1);
    is $message->TO_JSON =>
        {message => 'Hi Mum!', secret => 'I do not like eating healthily'};

# DESCRIPTION

[Moo::Role::ToJSON](https://metacpan.org/pod/Moo::Role::ToJSON) is a [Moo::Role](https://metacpan.org/pod/Moo::Role) which provides a ["TO\_JSON"](#to_json) method for
your classes. The `TO_JSON` method will returns a `HASH` reference of all the
["serializable\_attributes"](#serializable_attributes). It is your responsibility to ensure the attributes
in your classes can be directly encoded into `JSON`.

# ATTRIBUTES

[Moo::Role::ToJSON](https://metacpan.org/pod/Moo::Role::ToJSON) implements the following attributes.

## serializable\_attributes

    # optionally override serialized attributes on instantiation
    my $message = My::Message->new(
        serializable_attributes => [qw/feel_like_sharing message secret/]
    );

An `ARRAY` reference of attributes to serialize. Typically this would be set
directly in your class, but the default attributes can be overridden per
instance as in the example above.

This attribute is provided as a `lazy` [Moo](https://metacpan.org/pod/Moo) attribute, as such, the
["\_build\_serializable\_attributes"](#_build_serializable_attributes) builder should be used to set the default
serializable attributes.

All of the attributes must return data that can be directly encoded into JSON.

# METHODS

[Moo::Role::ToJSON](https://metacpan.org/pod/Moo::Role::ToJSON) implements the following methods.

## \_build\_serializable\_attributes

    sub _build_serializable_attributes { [qw/message secret/] }

The builder method returning the list of attributes to serialize. This method
must return an `ARRAY` reference.

## TO\_JSON

    use Mojo::JSON 'encode_json';

    my $message = My::Message->new();
    say encode_json $message;

Returns a `HASH` reference representing your object. This is intended to be
used by any `encode_json` function that checks for the availability of the
`TO_JSON` method for blessed objects.

## EXAMPLES

See `t/complete.t` for a complete example.

# AUTHOR

Paul Williams <kwakwa@cpan.org>

# COPYRIGHT

Copyright 2018- Paul Williams

# LICENSE

This library is free software; you can redistribute it and/or modify
it under the same terms as Perl itself.

# SEE ALSO

[DBIx::Class::Helper::Row::ToJSON](https://metacpan.org/pod/DBIx::Class::Helper::Row::ToJSON),
[Moo::Role](https://metacpan.org/pod/Moo::Role).
