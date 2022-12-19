# 日期::假期 1.10 发布

> 原文：<https://dev.to/jonasbn/dateholidays-110-released-181o>

[Date::Holidays](https://metacpan.org/pod/Date::Holidays) 的 1.08 版本在测试套件上出现了一些问题，这导致了 CPAN 测试人员的大量失败报告，详情请参见[第 21 期](https://github.com/jonasbn/Date-Holidays/issues/21)。

这导致了 1.09 版，它解决了坏测试的问题。但同时，它显示了与[日期、假日、新西兰](https://metacpan.org/pod/Date::Holidays::NZ)和[日期、假日、SK](https://metacpan.org/pod/Date::Holidays::SK) 整合的问题，因此分别创建了问题 [#22](https://github.com/jonasbn/perl-date-holidays/issues/22) 和 [#23](https://github.com/jonasbn/perl-date-holidays/issues/23) 。

问题#22 现在已经在 1.10 版本中解决了，下一个版本是 1.11，它计划解决问题#23，除非出现其他问题。

Date::Holidays::NZ 的改编还支持在 Date::Holidays::NZ 中描述的区域参数。

因此，通过 Date::Holidays 检查新年在新西兰是否是假日:

```
use Date::Holidays;

my $dh = Date::Holidays->new( countrycode => 'nz' );

if ($dh->is_holiday(year => 2018, month => 1, day => 1) {
   print “It is\n”;
} 
```

特别是奥克兰地区(详见 Date::Holidays::NZ)。

```
use Date::Holidays;

my $dh = Date::Holidays->new( countrycode => 'nz' );

if ($dh->is_holiday(year => 2018, month => 1, day => 1, region => 2) {        
   print “In Auckland it is\n”;
} 
```

您还可以获得假期列表:

```
use Date::Holidays;
use Data::Dumper;

my $dh = Date::Holidays->new( countrycode => 'nz' );
my $holidays_hashref = $dh->holidays(year => 2018);

print STDERR Dumper $holidays_hashref;

$VAR1 = {
   '0206' => 'Waitangi Day',
   '0402' => 'Easter Monday',
   '0102' => 'Day after New Years Day',
   '1022' => 'Labour Day',
   '1226' => 'Boxing Day',
   '1225' => 'Christmas Day',
   '0330' => 'Good Friday',
   '0425' => 'ANZAC Day',
   '0604' => 'Queens Birthday',
   '0101' => 'New Years Day'
}; 
```

基于区域:

```
use Date::Holidays;
use Data::Dumper;

my $dh = Date::Holidays->new( countrycode => 'nz' );

my $holidays_hashref = $dh->holidays(year => 2018, region => 2);

print STDERR Dumper $holidays_hashref;

$VAR1 = {
   '0129' => 'Auckland Anniversary Day',
   '1022' => 'Labour Day',
   '0101' => 'New Years Day',
   '0402' => 'Easter Monday',
   '1225' => 'Christmas Day',
   '0330' => 'Good Friday',
   '1226' => 'Boxing Day',
   '0102' => 'Day after New Years Day',
   '0425' => 'ANZAC Day',
   '0206' => 'Waitangi Day',
   '0604' => 'Queens Birthday'
}; 
```

非常欢迎反馈，

jonasbn