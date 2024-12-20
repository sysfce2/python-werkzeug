===============
HTTP Exceptions
===============

.. automodule:: werkzeug.exceptions


Error Classes
=============

The following error classes exist in Werkzeug:

.. autoexception:: BadRequest

.. autoexception:: Unauthorized

.. autoexception:: Forbidden

.. autoexception:: NotFound

.. autoexception:: MethodNotAllowed

.. autoexception:: NotAcceptable

.. autoexception:: RequestTimeout

.. autoexception:: Conflict

.. autoexception:: Gone

.. autoexception:: LengthRequired

.. autoexception:: PreconditionFailed

.. autoexception:: RequestEntityTooLarge

.. autoexception:: RequestURITooLarge

.. autoexception:: UnsupportedMediaType

.. autoexception:: RequestedRangeNotSatisfiable

.. autoexception:: ExpectationFailed

.. autoexception:: ImATeapot

.. autoexception:: MisdirectedRequest

.. autoexception:: UnprocessableEntity

.. autoexception:: Locked

.. autoexception:: FailedDependency

.. autoexception:: PreconditionRequired

.. autoexception:: TooManyRequests

.. autoexception:: RequestHeaderFieldsTooLarge

.. autoexception:: UnavailableForLegalReasons

.. autoexception:: InternalServerError
    :members:

.. autoexception:: NotImplemented

.. autoexception:: BadGateway

.. autoexception:: ServiceUnavailable

.. autoexception:: GatewayTimeout

.. autoexception:: HTTPVersionNotSupported

.. autoexception:: ClientDisconnected

.. autoexception:: SecurityError


Baseclass
=========

All the exceptions implement this common interface:

.. autoexception:: HTTPException
   :members: get_response, __call__


Special HTTP Exceptions
=======================

Starting with Werkzeug 0.3 some of the builtin classes raise exceptions that
look like regular python exceptions (eg :exc:`KeyError`) but are
:exc:`BadRequest` HTTP exceptions at the same time.  This decision was made
to simplify a common pattern where you want to abort if the client tampered
with the submitted form data in a way that the application can't recover
properly and should abort with ``400 BAD REQUEST``.

Assuming the application catches all HTTP exceptions and reacts to them
properly a view function could do the following safely and doesn't have to
check if the keys exist::

    def new_post(request):
        post = Post(title=request.form['title'], body=request.form['body'])
        post.save()
        return redirect(post.url)

If `title` or `body` are missing in the form, a special key error will be
raised which behaves like a :exc:`KeyError` but also a :exc:`BadRequest`
exception.

.. autoexception:: BadRequestKeyError


Simple Aborting
===============

Sometimes it's convenient to just raise an exception by the error code,
without importing the exception and looking up the name etc.  For this
purpose there is the :func:`abort` function.

.. autofunction:: abort

If you want to use this functionality with custom exceptions you can
create an instance of the aborter class:

.. autoclass:: Aborter


Custom Errors
=============

As you can see from the list above not all status codes are available as
errors.  Especially redirects and other non 200 status codes that do not
represent errors are missing.  For redirects you can use the :func:`redirect`
function from the utilities.

If you want to add an error yourself you can subclass :exc:`HTTPException`::

    from werkzeug.exceptions import HTTPException

    class PaymentRequired(HTTPException):
        code = 402
        description = '<p>Payment required.</p>'

This is the minimal code you need for your own exception.  If you want to
add more logic to the errors you can override the
:meth:`~HTTPException.get_description`, :meth:`~HTTPException.get_body`,
:meth:`~HTTPException.get_headers` and :meth:`~HTTPException.get_response`
methods.  In any case you should have a look at the sourcecode of the
exceptions module.

You can override the default description in the constructor with the
``description`` parameter::

    raise BadRequest(description='Request failed because X was not present')
