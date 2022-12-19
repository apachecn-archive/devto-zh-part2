# 这就是使用遗留代码的感觉。

> 原文：<https://dev.to/theodesp/this-is-the-feeling-of-working-with-legacy-code-4273>

很久以前，当我开始我的开发职业生涯时，我有过使用现有代码的不幸和幸运的经历——这些代码是我继承的已经存在的代码。

现在的问题是，这段代码可能很棒，干净整洁，也可能在另一端；混乱，意大利面条风格，很难理解和工作。

您可能已经猜到了，我继承了后者...我非常绝望，于是我复制了一部分回家研究。

几年后，当我重新整理我的一些文件时，我发现了这些代码片段，今天我将向你们展示一个小样本，供下一代品尝。

这就是了。(稍微改造了一下)。尽情享受吧！

```
 <?php
// Search page
$mt_ttle = 'Search Results - ';
if(!$_GET['q']) $q = ''; else $q = furl($_GET['q']);
if(!$_GET['flt']) $flt = ''; else $flt = furl($_GET['flt']);
$qLen = strlen($q);
$con = '<div class="plainHdr pgHeader searchb">' . $srch_rslts . '</div>';

if ($qLen <= 3){
    if ($qLen == 0) $err = 'No Keyword was entered.'; else $err = 'Keyword is too short.';
    $con .= '
    <div class="grid3_2col">
        <h3 class="nr mb20">' . $err . '</h3>
        <div class="paraBig">Please make sure you enter a keyword which is longer than 3 characters.<br />Try again in the Search field above.<br /><br /></div>
        <div class="nr mt20">PROBLEM SEARCHING?<br />If you continue having this message please <a href="' . url() . '/report_a_problem">Report this Problem</a> and<br />we will get back to you the soonest possible.</div>
    </div>' . $signup_quick_links;

} else {

    $sExp = preg_split('/\s+/',$q);
    $secure_keywords = array();
    foreach ($sExp as $key=>$keyword){
        if (strlen($keyword) >= 3){ // cut off words that are less than 3 chars
            $secure_keywords[] = $keyword;
        }
    }
    $kwords = count($secure_keywords);
    foreach ($secure_keywords as $key=>$keyword){
        $keyword = sql_proof($keyword);
        $srchqr .= "`en_title` LIKE '%$keyword%' OR `gr_title` LIKE '%$keyword%' OR `keywords` LIKE '%$keyword%'";
        $blgqr .= "`blog_title` LIKE '%$keyword%' OR `description` LIKE '%$keyword%'";
        $prfqr .= "`pfTitle` LIKE '%$keyword%' OR `pfDesc` LIKE '%$keyword%'";
        if ($key != ($kwords - 1)){
            $srchqr .= " AND "; $blgqr .= " AND "; $prfqr .= " AND ";
        }
    }

    if ($flt != 'blog' && $flt != 'feature'){
        if ($flt == 'cat') {if($_GET['val']){$val = furl($_GET['val']); $prfqr = $prfqr . " AND `pfCategory`='$val'";}}
        $sqlCommand = "SELECT * FROM `profiles` WHERE $prfqr"; // ---------- : Profiles
        $query = mysqli_query($myConnection, $sqlCommand) or die ();
        $prfrows = mysqli_num_rows($query); $sdcats = array();
        if ($prfrows >= 1){
            while ($row = mysqli_fetch_array($query)){
                $pfTitle = $row['pfTitle']; $link_code = $row['link_code']; $pfDesc = $row['pfDesc']; $pfCategory = $row['pfCategory'];
                $srslts .= '<div><b><a href="' . url() . '/business/'.$link_code . '">'.$pfTitle . '</a></b><br />
                <span>' . ucwords(str_replace('_',' ',$pfCategory)) . '</span><br />' . $pfDesc . '</div>';
                $sdcats[] = $pfCategory;
            }
            asort($sdcats);
            $counts = array_count_values($sdcats);
            foreach($counts as $cuVal => $cuCount){
                $sqlCommand = "SELECT `en_title`, `gr_title` FROM `categories` WHERE `link_code`='$cuVal' LIMIT 1";
                $query = mysqli_query($myConnection, $sqlCommand) or die ();
                $row = mysqli_fetch_array($query); $caTtl = $row[$lang . '_title'];
                $sd_panel .= '<a class="sdl" href="' . curPageURL() . '&flt=cat&val=' . $cuVal . '">' . $caTtl . ' <span>(' . $cuCount . ')</span></a>';
            }
        }
    }

    if ($flt != 'cat' && $flt != 'feature'){
        $sqlCommand = "SELECT * FROM `blog` WHERE $blgqr"; // ---------- : Blogs
        $query = mysqli_query($myConnection, $sqlCommand) or die ();
        $blgrows = mysqli_num_rows($query);
        if ($blgrows >= 1){
            while ($row = mysqli_fetch_array($query)){
                $blg_title = $row['blog_title']; $link_code = $row['link_code']; $description = $row['description'];
                $srslts .= '<div><b><a href="' . url() . '/blog/'.$link_code . '">'.$blg_title . '</a></b><br /><span>Article</span><br />' . $description . '</div>';
            }
            $sd_panel .= '<a class="sdl" href="' . curPageURL() . '&flt=blog">Blog Posts <span>(' . $blgrows . ')</span></a>';
        }
    }

    if ($flt != 'cat' && $flt != 'blog'){
        $sqlCommand = "SELECT * FROM `categories` WHERE $srchqr"; // ---------- : Features
        $query = mysqli_query($myConnection, $sqlCommand) or die ();
        $schrows = mysqli_num_rows($query);
        if ($schrows >= 1){
            while ($row = mysqli_fetch_array($query)){
                $catTitle = $row[$lang . '_title']; $link_code = $row['link_code']; $catDescr = $row[$lang . '_descr'];
                $srslts .= '<div><b><a href="' . url() . '/' . $link_code . '">' . $catTitle . '</a></b><br /><span>Feature</span><br />' . $catDescr . '</div>';
            }
            $sd_panel .= '<a class="sdl" href="' . curPageURL() . '&flt=feature">Features <span>(' . $schrows . ')</span></a>';
        }
    }

    $numrows = $schrows + $prfrows + $blgrows;

    if ($flt != ''){
        $sd_panel = '<a class="sdl" href="' . url() . '/search?q=' . $q . '">Show All Results</a>';
    }

    if ($numrows >= 1) {
        if ($numrows > 1) $rescap = 's'; 
        if ($flt == '') $sfhd = '<div class="nr mb10 sdtls"><b>' . $fltr_ct . '</b></div>';
        $con .= '<div class="nr midHd mb20">' . $numrows . ' Result' . $rescap . ' Found for <b>“' . $q . '”</b></div>
        <div class="sdclmn">' . $sfhd . '<div class="nr bt1">' . $sd_panel . '</div></div>
        <div class="rgtpnl"><div class="nr mb10 sdtls"><b>Results</b></div><div class="nr bt1 scrlts">' . $srslts . '</div></div>';

    } else {
        $con .= '<h3 class="nr mb20">No Results Found</h3>' . $signup_quick_links;
    }
}
?> 
```

Enter fullscreen mode Exit fullscreen mode