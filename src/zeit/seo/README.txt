================================
Search engine optimisation (SEO)
================================

This is a small package containing views and adapters for search engingne
optimisation.

Browser Tests
=============

>>> import zope.testbrowser.testing
>>> browser = zope.testbrowser.testing.Browser()
>>> browser.addHeader('Authorization', 'Basic user:userpw')

Open the test content and go to the SEO page:

>>> browser.open('http://localhost/++skin++cms/repository/testcontent')
>>> browser.getLink('SEO').click()
>>> print browser.title.strip()
testcontent – View SEO data

To edit the SEO data we need to check the object out:

>>> browser.getLink('Checkout').click()
>>> print browser.title.strip()
testcontent – Edit SEO data

Fill out the form:

>>> browser.getControl('HTML title').value = 'HTML title'
>>> browser.getControl('HTML description').value = 'HTML description'
>>> browser.getControl('Ressort').displayValue = ['Deutschland']
>>> browser.getControl('Meta robots').value = 'noindex'
>>> browser.getControl('Keyword entity type').displayValue = ['free']
>>> browser.getControl('Disable intext links').click()
>>> browser.getControl('Apply').click()

Verify the source:

>>> browser.getLink('Source').click()
>>> print browser.getControl('Source').value.replace('\r', '')
<testtype>
  <head>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="date_last_checkout">...</attribute>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="html-meta-title">HTML title</attribute>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="html-meta-description">HTML description</attribute>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="html-meta-robots">noindex</attribute>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="html-meta-hide-timestamp">no</attribute>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="seo-disable-intext-links">yes</attribute>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="seo-keyword-entity-type">free</attribute>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="ressort">Deutschland</attribute>
    <attribute xmlns:py="http://codespeak.net/lxml/objectify/pytype" py:pytype="str" ns="http://namespaces.zeit.de/CMS/document" name="channels">Deutschland</attribute>
  </head>
  <body/>
</testtype>
<BLANKLINE>


Go back to the SEO tab and check in. We're still at the SEO view then:

>>> browser.getLink('SEO').click()
>>> browser.getLink('Checkin').click()
>>> print browser.title.strip()
testcontent – View SEO data


Python level tests
==================

There is an adapter from ICMSContent to ISEO. The SEO information is stored in
WebDAV properties which each and every content type has. The views are
registered on ICommonMetadata because in reality it doesn't make sense to add
SEO information for other types (such as images).

Create an unknown resource and adapt it to ISEO:

>>> import zeit.seo.interfaces
>>> import zeit.cms.repository.unknown
>>> content = zeit.cms.repository.unknown.UnknownResource(u'')
>>> seo = zeit.seo.interfaces.ISEO(content)
>>> seo
<zeit.seo.seo.SEO object at 0x...>


Let's set the title and description:

>>> seo.html_title = u'Special title'
>>> seo.html_description = u'Very special description'

The properties are now set at `content`:

>>> properties = zeit.connector.interfaces.IWebDAVProperties(content)
>>> properties[(u'html-meta-title', u'http://namespaces.zeit.de/CMS/document')]
u'Special title'
>>> properties[(u'html-meta-description',
...             u'http://namespaces.zeit.de/CMS/document')]
u'Very special description'
